---
layout: post
title: "openssl DRBG(NIST sp800-90A) 분석"
categories: security
tags: openssl
---

(미완)

## DRBG

Deterministic Random Bit Generator의 축약어로 NIST에서 sp800-90A 표준인 유사난수를 생성하는 방법입니다. 난수 생성간에 Hash, hmac, AES, TDES를 사용할 수 있습니다.

![image](https://user-images.githubusercontent.com/32065940/107867463-5f800880-6ebe-11eb-96e0-94ec92b4b76a.png)

위의 그림은 DRBG 기능 모델을 보여줍니다. 기본적으로 Internal State를 가지고 난수를 반복적으로 생성하며 또한 Internal State 업데이트를 수행합니다. 초기 Internal State는 난수(Entropy)와 옵션 값(Nonce, Personalization String)을 활용하여 seed를 생성하여 업데이트를 하며 사용자가 원할 시 Internal State는 언제든지 Reseed 함수를 통해 다시 seed를 생성, 업데이트 가능합니다.



## Openssl

본 글에서는 OpenSSl-v1.1.1을 기준으로 AES를 이용한 CTR-DRBG 위주로 서술합니다.

 ### 사용법

```c++
#include "openssl/rand_drbg.h"
#include "openssl/err.h"

#define USE_DF					(1)
#define PREDICTION_RESISTANCE	(0)
#define ENTROPYLEN				(128/8)
#define NONCELEN				(64/8)
#define PERSLEN					(0/8)
#define ADINLEN					(0/8)
#define OUTLEN					(512/8)

unsigned char pers[] = "";
unsigned char adin_1[] = "";
unsigned char adin_2[] = "";
unsigned char ENTROPY[] = "\x89\x0e\xb0\x67\xac\xf7\x38\x2e\xff\x80\xb0\xc7\x3b\xc8\x72\xc6";
unsigned char NONCE[] = "\xaa\xd4\x71\xef\x3e\xf1\xd2\x03";
unsigned char out[] = "";
char err[120] = "";

size_t get_entropy(RAND_DRBG* drbg,
	unsigned char** pout,
	int entropy, size_t min_len,
	size_t max_len,
	int prediction_resistance) 
{
	*pout = ENTROPY;
	return ENTROPYLEN;

}
void cleanup_entropy(RAND_DRBG* ctx,
	unsigned char* out, size_t outlen) 
{

}
size_t get_nonce(RAND_DRBG* drbg, unsigned char** pout,
	int entropy, size_t min_len,
	size_t max_len) 
{
	*pout = NONCE;
	return NONCELEN;
}
void cleanup_nonce(RAND_DRBG* drbg,
	unsigned char* out, size_t outlen) 
{

}

int main() {
    /// must specify which AES is used and whether derivation function is used or not
	RAND_DRBG* drbg = RAND_DRBG_new(NID_aes_128_ctr, USE_DF ? 0 : RAND_DRBG_FLAG_CTR_NO_DF, NULL);

    /// for test, must disable reseed time interval feature
	RAND_DRBG_set_reseed_time_interval(drbg, 0);
    /// for test, set either entropy or nonce to intended value 
	RAND_DRBG_set_callbacks(drbg, get_entropy, cleanup_entropy, get_nonce, cleanup_nonce);

    /// in case of drbg_vectors_no_reseed test, the process is 'instantiate -> generate -> generate'
	if (!RAND_DRBG_instantiate(drbg, pers, PERSLEN)) {
		ERR_error_string(ERR_get_error(), err);
		fprintf(stderr, "Instantiate error : %s\n", err);
	}
	if (!RAND_DRBG_generate(drbg, out, OUTLEN, PREDICTION_RESISTANCE, adin_1, ADINLEN)) {
		ERR_error_string(ERR_get_error(), err);
		fprintf(stderr, "#1 Generate error : %s\n", err);
	}
	if (!RAND_DRBG_generate(drbg, out, OUTLEN, PREDICTION_RESISTANCE, adin_2, ADINLEN)) {
		ERR_error_string(ERR_get_error(), err);
		fprintf(stderr, "#2 Generate error : %s\n", err);
	}

	for (int i = 0; i < OUTLEN; i++) {
		printf("%02X", out[i]);
	}
	printf("\n");
	/// the result is "A5514ED7095F64F3D0D3A5760394AB42062F373A25072A6EA6BCFD8489E94AF6CF18659FEA22ED1CA0A9E33F718B115EE536B12809C31B72B08DDD8BE1910FA3"
}
```

위의 코드는 openssl을 활용하여 NIST CAVP KAT 중 no_reseed 테스트에서 [AES-128 use df]인 경우 중 가장 첫 번째 케이스를 수행하는 코드입니다. 

1. RAND_DRBG_new : RAND_DRBG 구조체를 통해 internal state를 유지하게 됩니다. 구조체를 초기화하면서 메모리를 할당해 주는 함수입니다. AES 종류와 플래그(실질적으로 derivation functio의 사용 유무만을 가지고 있음) 그리고 parent drbg(entropy를 받아올 상위 RAND_DRBG 구조체)를 설정합니다.

   > 테스트를 수행하기 위해서는 AES 종류와 플래그는 케이스에 맞게 설정이 되어야 하며 parent drbg는 NULL로 초기화 되어야 합니다.

2. RAND_DRBG_set_callbacks : entropy와 nonce를 받아오고 해제하는 callback 함수를 설정해주는 함수입니다. openssl에서는 기본적으로 OS별로 난수를 생성해내는 라이브러리를 활용합니다.

   > 테스트를 수행하기 위해서는 테스트 케이스의 entropy와 nonce값을 사용해야 하기 때문에 callback 함수를 따로 지정해야 합니다.

3. RAND_DRBG_instantiate : 표준에서 instantiate에 해당하는 함수입니다.

4. RAND_DRBG_generate : 표준에서 generate에 해당하는 함수입니다.

5. ERR_error_string : Error 코드를 이해할 수 있는 문자열로 변환해주는 함수입니다.

6. ERR_get_error : Error 코드를 반환해주는 함수입니다.



### 핵심 코드 분석

DRBG에서 사용되는 함수로는 Instantiate, Reseed, Uninstantiate, Generate이 있다. 해당 함수들은 각 표준에 서술된 내용을 수행합니다. 조건을 확인하고 초기화를 진행하며 서술된 사항을 따라 진행합니다. 이런 내용의 코드는 따라가는데 어렵지 않을 것이라 예상되어 분석이 난해한 Update 함수와 Derivation function그리고 BCC를 설명하겠습니다.

1. update

```c
__owur static int ctr_update(RAND_DRBG *drbg,
                             const unsigned char *in1, size_t in1len,
                             const unsigned char *in2, size_t in2len,
                             const unsigned char *nonce, size_t noncelen)
{
    RAND_DRBG_CTR *ctr = &drbg->data.ctr;
    int outlen = AES_BLOCK_SIZE;
    unsigned char V_tmp[48], out[48];
    unsigned char len;

    /* correct key is already set up. */
    memcpy(V_tmp, ctr->V, 16);
    inc_128(ctr);
    memcpy(V_tmp + 16, ctr->V, 16);
    if (ctr->keylen == 16) {
        len = 32;
    } else {
        inc_128(ctr);
        memcpy(V_tmp + 32, ctr->V, 16);
        len = 48;
    }
    /// 반복문을 돌며 하나씩 increment하면서 암호화 하는 것을 V_tmp에 블락단위로 채운 후 한 번의 호출로 해결
    if (!EVP_CipherUpdate(ctr->ctx_ecb, out, &outlen, V_tmp, len)
            || outlen != len)
        return 0;
    /// 먼저 복사 후 xor 과정은 후에 ctr_XOR 함수를 통해서 수행
    memcpy(ctr->K, out, ctr->keylen);
    memcpy(ctr->V, out + ctr->keylen, 16);

    if ((drbg->flags & RAND_DRBG_FLAG_CTR_NO_DF) == 0) {
        /* If no input reuse existing derived value */
        if (in1 != NULL || nonce != NULL || in2 != NULL)
            if (!ctr_df(ctr, in1, in1len, nonce, noncelen, in2, in2len))
                return 0;
        /* If this a reuse input in1len != 0 */
        if (in1len)
            ctr_XOR(ctr, ctr->KX, drbg->seedlen);
    } else {
        ctr_XOR(ctr, in1, in1len);
        ctr_XOR(ctr, in2, in2len);
    }

    if (!EVP_CipherInit_ex(ctr->ctx_ecb, NULL, NULL, ctr->K, NULL, -1)
        || !EVP_CipherInit_ex(ctr->ctx_ctr, NULL, NULL, ctr->K, NULL, -1))
        return 0;
    return 1;
}
```

첫 번째로 openssl에서는 암호화를 3단계를 거쳐서 수행합니다 (EVP_CipherInit_ex -> EVP_CipherUpdate -> EVP_CipherFinal_ex)

> 참조 : [/docs/man1.1.0/man3/EVP_CipherInit_ex.html (openssl.org)](https://www.openssl.org/docs/man1.1.0/man3/EVP_CipherInit_ex.html)

두 번째로 표준에서는 각 함수별로 derviation function(앞으로 df로 칭하겠습니다)의 사용 유무에 따라서 각 알고리즘 구현 함수를 따로 두는 반면에 openssl에서는 df 사용 여부를 update 함수안에 구현하였습니다.

세 번째로 df의 결과는 ctr->KX라는 버퍼에 담기게 되며 ctr_XOR 함수를 통해서 K, V에 xor 됩니다.



2. df

```c
__owur static int ctr_df(RAND_DRBG_CTR *ctr,
                         const unsigned char *in1, size_t in1len,
                         const unsigned char *in2, size_t in2len,
                         const unsigned char *in3, size_t in3len)
{
    static unsigned char c80 = 0x80;
    size_t inlen;
    unsigned char *p = ctr->bltmp;
    int outlen = AES_BLOCK_SIZE;

    if (!ctr_BCC_init(ctr))
        return 0;
    if (in1 == NULL)
        in1len = 0;
    if (in2 == NULL)
        in2len = 0;
    if (in3 == NULL)
        in3len = 0;
    inlen = in1len + in2len + in3len;
    /* Initialise L||N in temporary block */
    *p++ = (inlen >> 24) & 0xff;
    *p++ = (inlen >> 16) & 0xff;
    *p++ = (inlen >> 8) & 0xff;
    *p++ = inlen & 0xff;

    /* NB keylen is at most 32 bytes */
    *p++ = 0;
    *p++ = 0;
    *p++ = 0;
    *p = (unsigned char)((ctr->keylen + 16) & 0xff);
    ctr->bltmp_pos = 8;
    if (!ctr_BCC_update(ctr, in1, in1len)
        || !ctr_BCC_update(ctr, in2, in2len)
        || !ctr_BCC_update(ctr, in3, in3len)
        || !ctr_BCC_update(ctr, &c80, 1)
        || !ctr_BCC_final(ctr))
        return 0;
    /* Set up key K */
    if (!EVP_CipherInit_ex(ctr->ctx_ecb, NULL, NULL, ctr->KX, NULL, -1))
        return 0;
    /* X follows key K */
    if (!EVP_CipherUpdate(ctr->ctx_ecb, ctr->KX, &outlen, ctr->KX + ctr->keylen,
                          AES_BLOCK_SIZE)
        || outlen != AES_BLOCK_SIZE)
        return 0;
    if (!EVP_CipherUpdate(ctr->ctx_ecb, ctr->KX + 16, &outlen, ctr->KX,
                          AES_BLOCK_SIZE)
        || outlen != AES_BLOCK_SIZE)
        return 0;
    if (ctr->keylen != 16)
        if (!EVP_CipherUpdate(ctr->ctx_ecb, ctr->KX + 32, &outlen,
                              ctr->KX + 16, AES_BLOCK_SIZE)
            || outlen != AES_BLOCK_SIZE)
            return 0;
    return 1;
}
```

df 함수는 BCC 과정이 나오면서 다소 복잡합니다. 제일 첫 번째로 알아야 할 사항은 ctx 구조체가 들고 다니는 버퍼와 변수의 쓰임입니다. BCC 과정을 수행하기 위해서 bltmp 버퍼와 bltmp_pos 변수가 사용되고 BCC의 결과는 KX 버퍼에 담기게 됩니다. 표준에서는 BCC 과정을 반복 수행하지만 openssl 에서는 버퍼를 통해서 반복문 없이 수행할 수 있도록 구현이 되있습니다.



3. BCC

BCC 코드를 이해하기 위해서는 bltmp, KX 이 두 버퍼의 구조에 대해서 우선 이해를 해야합니다.

```c
__owur static int ctr_BCC_init(RAND_DRBG_CTR *ctr)
{
    unsigned char bltmp[48] = {0};
    unsigned char num_of_blk;

    memset(ctr->KX, 0, 48);
    num_of_blk = ctr->keylen == 16 ? 2 : 3;
    bltmp[(AES_BLOCK_SIZE * 1) + 3] = 1;
    bltmp[(AES_BLOCK_SIZE * 2) + 3] = 2;
    return ctr_BCC_block(ctr, ctr->KX, bltmp, num_of_blk * AES_BLOCK_SIZE);
}

```


---
title: 'String'
description: 'd'
order: 4
---

### 字符串hash

#### 双hash
```cpp
const int maxn = 2.1e6 + 5;

int base[3] = {0, 131, 13131};
const LL P = 998244353;

LL inv(LL c) {
    LL a = c, b = P, x = 1, y = 0;
    while (b) {
        int t = a / b;
        a -= t * b; swap(a, b);
        x -= t * y; swap(x, y);
    }
    return (x % P + P) % P;
}
int invfact1[maxn], invfact2[maxn];
void init(int n) {
    invfact1[0] = invfact2[0] = 1;
    LL invbase1 = inv(base[1]), invbase2 = inv(base[2]);
    LL tmp1 = 1, tmp2 = 1;
    for (int i = 1; i <= n; i++) {
        invfact1[i] = tmp1 = tmp1 * invbase1 % P;
        invfact2[i] = tmp2 = tmp2 * invbase2 % P;
    }
}

template<const int mod>
struct String_Hash {
    static const int P = mod;
    int n;
    vector<LL> d1, d2;

    String_Hash () : n(0) {}
    String_Hash(char *s) {
        init(s);
    }
    void init(char *s) {
        n = strlen(s);
        d1.assign(n + 5, 0);
        d2.assign(n + 5, 0);
        LL p1 = 1, p2 = 1;
        for (int i = 1; i <= n; i++) {
            d1[i] = (d1[i - 1] + p1 * (*(s + i - 1) - 64) % P) % P;
            (p1 *= base[1]) %= P;
            d2[i] = (d2[i - 1] + p2 * (*(s + i - 1) - 64) % P) % P;
            (p2 *= base[2]) %= P;
        }
    }
    pair<LL, LL> gets(int l, int r) {
        LL s1, s2;
        s1 = (d1[r] - d1[l - 1]) * invfact1[l - 1] % P;
        s2 = (d2[r] - d2[l - 1]) * invfact2[l - 1] % P;
        (s1 += P) %= P, (s2 += P) %= P;
        return {s1, s2};
    }
};
using stringhash = String_Hash<P>;
```

#### 大质数单hash
```cpp
const uLL mod = (1ull << 61) - 1;
mt19937_64 rnd(chrono::steady_clock::now().time_since_epoch().count());
uniform_int_distribution<uLL> dist(mod / 2, mod - 1);
const uLL base = dist(rnd);

const int maxn = 1e6 + 5;

uLL power[maxn];

void init(int n) {
    power[0] = 1;
    for(int i = 1; i < n; i++)
        power[i] = power[i - 1] * base % mod;
}

struct String_Hash {
    int n;
    vector<uLL> hashed;

    String_Hash() : n(0) {}
    String_Hash(char *s) {
        init(s);
    }
    void init(char *s) {
        n = strlen(s);
        hashed.assign(n + 5, 0);
        for (int i = 0; i < n; i++)
            hashed[i + 1] = add(mul(hashed[i], base), s[i]);
    }

    static inline uLL add(uLL a, uLL b) {
        a += b;
        if (a >= mod) a -= mod;
        return a;
    }
    static inline uLL mul(uLL a, uLL b) {
        __uint128_t c = __uint128_t(a) * b;
        return add(c >> 61, c & mod);
    }
    uLL merge(uLL h1, uLL h2, int len2) {
        return add(mul(h1, power[len2]), h2);
    }
    uLL query(int l, int r) {
        return add(hashed[r], mod - mul(hashed[l - 1], power[r - l + 1]));
    }
};
```
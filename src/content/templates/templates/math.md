---
title: 'Math'
description: 'c'
order: 3
---

### 模数

```cpp
#include<bits/stdc++.h>
#define LL long long
using namespace std;
const int P = 1e9 + 7;
template<const int mod>
struct ModInt {
    static const int P = mod;
    int x;
    ModInt (int x = 0) : x(x % P) {}
    ModInt (LL x) : x(int(x % P)) {}
    int val() {return x;}
    
    ModInt operator + (const ModInt &a) const {int x0 = x + a.x; return ModInt(x0 < P ? x0 : x0 - P);}
    ModInt operator - (const ModInt &a) const {int x0 = x - a.x; return ModInt(x0 < 0 ? x0 + P : x0);}
    ModInt operator * (const ModInt &a) const {return ModInt(1ll * x * a.x % P);}
    ModInt operator / (const ModInt &a) const {return *this * a.inv();}
    // ModInt & operator ++ () {++x; return *this;}
    // ModInt & operator -- () {--x; return *this;}
    bool operator == (const ModInt &a) const {return x == a.x;};
    bool operator != (const ModInt &a) const {return x != a.x;};
    void operator += (const ModInt &a) {x += a.x; if (x >= P) x -= P;}
    void operator -= (const ModInt &a) {x -= a.x; if (x < 0) x += P;}
    void operator *= (const ModInt &a) {x = 1ll * x * a.x % P;}
    void operator /= (const ModInt &a) {*this = *this / a;}
    friend ModInt operator + (int y, const ModInt &a){int x0 = y + a.x; return ModInt(x0 < P ? x0 : x0 - P);}
    friend ModInt operator - (int y, const ModInt &a){int x0 = y - a.x; return ModInt(x0 < 0 ? x0 + P : x0);}
    friend ModInt operator * (int y, const ModInt &a){return ModInt(1ll * a.x * y % P);}
    friend ModInt operator / (int y, const ModInt &a){return ModInt(y) / a;}
    friend ostream &operator<<(ostream &os, const ModInt &a) {return os << (a.x + P) % P;}
    friend istream &operator>>(istream &is, ModInt &t) {return is >> t.x;}

    ModInt pow(LL n) const {
        ModInt sum(1), base(x);
        n %= (P - 1);
        while (n) {
            if (n & 1) sum *= base;
            base *= base;
            n >>= 1;
        }
        return sum;
    }
    
    ModInt inv() const {
        int a = x, b = P, x = 1, y = 0;
        while (b) {
            int t = a / b;
            a -= t * b; swap(a, b);
            x -= t * y; swap(x, y);
        }
        if (x < 0) y += P;
        return x;
    }
};
using mint = ModInt<P>;

int main()
{
    cin.tie(0);
    cout.tie(0);
    ios::sync_with_stdio(0);

    return 0;
}
```

### 组合数

```cpp
const int P = 1e9 + 7;
template<const int mod>
struct ModInt {
    static const int P = mod;
    int x;
    ModInt (int x = 0) : x(x % P) {}
    ModInt (LL x) : x(int(x % P)) {}
    int val() {return x;}

    ModInt operator + (const ModInt &a) const {int x0 = x + a.x; return ModInt(x0 < P ? x0 : x0 - P);}
    ModInt operator - (const ModInt &a) const {int x0 = x - a.x; return ModInt(x0 < 0 ? x0 + P : x0);}
    ModInt operator * (const ModInt &a) const {return ModInt(1ll * x * a.x % P);}
    ModInt operator / (const ModInt &a) const {return *this * a.inv();}
    // ModInt & operator ++ () {++x; return *this;}
    // ModInt & operator -- () {--x; return *this;}
    bool operator == (const ModInt &a) const {return x == a.x;};
    bool operator != (const ModInt &a) const {return x != a.x;};
    void operator += (const ModInt &a) {x += a.x; if (x >= P) x -= P;}
    void operator -= (const ModInt &a) {x -= a.x; if (x < 0) x += P;}
    void operator *= (const ModInt &a) {x = 1ll * x * a.x % P;}
    void operator /= (const ModInt &a) {*this = *this / a;}
    friend ModInt operator + (int y, const ModInt &a){int x0 = y + a.x; return ModInt(x0 < P ? x0 : x0 - P);}
    friend ModInt operator - (int y, const ModInt &a){int x0 = y - a.x; return ModInt(x0 < 0 ? x0 + P : x0);}
    friend ModInt operator * (int y, const ModInt &a){return ModInt(1ll * a.x * y % P);}
    friend ModInt operator / (int y, const ModInt &a){return ModInt(y) / a;}
    friend ostream &operator<<(ostream &os, const ModInt &a) {return os << (a.x + P) % P;}
    friend istream &operator>>(istream &is, ModInt &t) {return is >> t.x;}

    ModInt pow(LL n) const {
       ModInt sum(1), base(x);
       n %= (P - 1);
       while (n) {
           if (n & 1) sum *= base;
           base *= base;
           n >>= 1;
       }
       return sum;
    }

    ModInt inv() const {
        int a = x, b = P, x = 1, y = 0;
        while (b) {
        int t = a / b;
           a -= t * b; swap(a, b);
           x -= t * y; swap(x, y);
        }
        if (x < 0) y += P;
        return x;
    }
};
using mint = ModInt<P>;

const int maxn = 1e6 + 5;
mint fact[maxn], invfact[maxn];

void init(int n) {
    fact[0] = invfact[0] = 1;
    for (int i = 1; i <= n; i++) fact[i] = fact[i - 1] * i;
    
    invfact[n] = fact[n].inv();
    for (int i = n - 1; i; i--)
        invfact[i] = invfact[i + 1] * (i + 1);
}
mint C(int a, int b) {
    if (a == b || !b) return 1;
    if (a < b) return 0;
    return fact[a] * invfact[b] * invfact[a - b];
}
```

### 矩阵快速幂
```cpp
struct matrix {
    int n;
    vector<vector<mint> >a;
    
    matrix(int x) {
        init(x);
    }
    void init(int x) {
        n = x;
        a.assign(n + 5, vector<mint> (n + 5, 0));
    }
    matrix operator * (const matrix &x) const {
        matrix t(n);
        for (int i = 1; i <= n; i++)
            for (int j = 1; j <= n; j++)
                for (int k = 1; k <= n; k++)
                    t.a[i][j] += a[i][k] * x.a[j][k];
        return t;
    }
};
```

### 逆元

#### 线性求逆元

```cpp
const int mod = 1e9 + 7;
const int maxn = 1e6 + 5;

int inv[maxn];

void init(int n) {
    inv[0] = inv[1] = 1;
    for (int i = 2; i <= n; i++)
        inv[i] = 1ll * inv[mod % i] * (mod - mod / i) % mod;
}
```

#### 扩展欧几里得

```cpp
void exgcd(LL a, LL b, LL &x, LL &y) {
    if (!b) return (void)(x = 1, y = 0);
    exgcd(b, a % b, y, x);
    y -= a / b * x;
}

const int mod = 1e9 + 7;

LL inv(LL a) {
  if (a == 1) return 1;
  return inv(mod % a) * (mod - mod / a) % mod;
}
```


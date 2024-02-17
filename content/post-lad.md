+++
title = "地理座標系と平面直角座標系の変換"
template = "page.html"
date = 2024-02-17T15:00:00Z
[taxonomies]
tags = ["math", "typescript"]
[extra]
summary = "緯度経度で表される地理座標系を，メートルのx,y座標に変換する関数を定義した．"
mathjax = "tex-mml"
+++

緯度経度で表される地理座標系を，メートルのx,y座標に変換したい．



## 前提

なんでもいいが，ここでは処理はTypeScriptで記述する．

以下の，地理座標系を表す`GetCoordinate`型を，平面直交座標系を表す`PlaneCoordinate`型に変換することを考える．

```TypeScript
interface GeoCoordinate {
  latitude: number;
  longitude: number;
};

interface PlaneCoordinate {
  x: number;
  y: number;
};

const GeoToPlane: PlaneCoordinate = (geo: GeoCoordinate) => {
  //奇跡的な処理
  return {x: hoge, y:huga};
};
```

球形（しかも楕円）の表面上の距離を単純な平面の直角座標に変換するので，難儀である．どう考えても面倒．




## 真面目にやらない

日本においては，緯度に110000を，経度に91000をかけるとだいたいいい感じの値になることが知られている．

```TypeScript
const GeoToPlaneDodge: PlaneCoordinate = (geo: GeoCoordinate) => {
  return {x: geo.latitude * 110000, y:geo.latitude * 91000};
};
```

これは，日本の（多分東京の）緯度経度から概算された値を定数として使っているだけなので，より高精度の値が必要な場合や，海外で使用する場合は当然NG．


## 真面目にやる

ちゃんと地球の形状を考慮して，真面目に変換する．

一方で，地球の完璧な形状をほげするのは不可能なので，人間が扱える図形に近似して考える．具体的には，回転楕円体として扱う．ここで，パラメータは測地基準系1980（GRS80）を採用する．

- 長半径: 6378137
- 扁平率の逆数: 298.257222101

また，平面直角座標系の原点に関しては，

（[https://www.gsi.go.jp/sokuchikijun/datum-main.html](https://www.gsi.go.jp/sokuchikijun/datum-main.html)等．）

### 数式を並べる


[https://vldb.gsi.go.jp/sokuchi/surveycalc/surveycalc/algorithm/bl2xy/bl2xy.htm](https://vldb.gsi.go.jp/sokuchi/surveycalc/surveycalc/algorithm/bl2xy/bl2xy.htm) 参照．

#### パラメータ：

$\phi$:緯度[$rad$]，$\lambda$:経度[$rad$]

```typescript
interface GeoCoordinate {
  latitude: number;
  longitude: number;
};
```

$\phi_0,\lambda_0$: 平面直角座標系原点の緯度経度[$rad$]

```typescript
const phi_0:number = hoge;

const lambda_0:number = hoge;
```
$a=$6378131:楕円体長半径[$m$]

$F=$298.257222101:楕円体の扁平率の逆数

$m_0=$0.9999:平面直角座標系の$x$軸における縮尺係数

```typescript
const a:number = 6378131;

const F:number = 298.257222101;

const m_0:number = 0.9999;
```


#### 計算

$\phi, \lambda$を受け取り，平面直角座標$x,y$を返す．

$n$:

$$n = \frac{1}{2F-1}$$

```typescript
const n = 1 / (2*F - 1);
```

$A_i(i=0,1,...,5)$:

$$A_0 = 1 + \frac{n^2}{4} + \frac{n^3}{64}$$

$$A_1 = -\frac{3}{2}\left( n - \frac{n^3}{8} - \frac{n^5}{64} \right)$$

$$A_2 = \frac{15}{16}\left( n^2 - \frac{n^4}{4} \right)$$

$$A_3 = -\frac{35}{48}\left( n^3 - \frac{5n^5}{16} \right)$$

$$A_4 = \frac{315}{512}n^4$$


$$A_5 = \frac{693}{1280}n^5$$

```typescript
const A_List: number[] = [
  1 + (n^2 / 4) + (n^3 / 64),
  -(3/2) * (n - ((n^3) / 8) - ((n^5) / 64)),
  (15/16) * (n^2 - ((n^4) / 4)),
  -(35/48) * (n^3 - ((5*n^5) / 16)),
  315/512 * n^4,
  693/1280 * n^5
];
```


$\alpha_i(i=1,2,...,5)$:

$$\alpha_1 = \frac{1}{2}n - \frac{2}{3}n^2 + \frac{5}{16}n^3 - \frac{41}{180}n^4 - \frac{127}{288}n^5$$

$$\alpha_2 = \frac{13}{48}n^2 - \frac{3}{5}n^3 + \frac{557}{1440}n^4 + \frac{281}{630}n^5$$

$$\alpha_3 = \frac{61}{240}n^3 - \frac{103}{140}n^4 + \frac{15061}{26880}n^5$$

$$\alpha_4 = \frac{49561}{161280}n^4 - \frac{179}{168}n^5$$

$$\alpha_5 = \frac{34729}{80640}n^5$$


```typescript
const alpha_List:number[] = [
  (1/2)*n - (2/3)*(n^2) + (5/16)*(n^3) - (41/180)*(n^4) - (127/288)*(n^5),
  (13/48)*(n^2) - (3/5)*(n^3) + (557/1440)*(n^4) + (281/630)*(n^5),
  (61/240)*(n^3) - (103/140)*(n^4) + (15061/26880)*(n^5),
  (49561/161280)*(n^4) - (179/168)*(n^5),
  (34729/80640)*(n^5)
];
```

$\bar{S_{\phi_0}},\bar{A}$:

$$\bar{S_{\phi_0}} = \frac{m_0a}{1+n}\left( A_0\phi_0 + \Sigma^5_{j=1} A_j \sin{(2j\phi_0)} \right)$$

$$\bar{A} = \frac{m_0a}{1+n}A_0$$

```typescript
const bar_S_phi_0 = (m_0*a / (1+n)) * (A_0*phi_0 + A_List.map((A, j) => A * Math.sin(2*j*phi_0) ).reduce((a, b) => a+b, 0));

const bar_A = (m_0 / (1+n)) * A_0;
```


$\lambda_\epsilon, \lambda_s$:

$$\lambda_\epsilon = \cos(\lambda - \lambda_0)$$

$$\lambda_s = \sin(\lambda - \lambda_0)$$


```typescript
const lambda_epsilon = Math.cos(lambda - lambda_0);

const lambda_s = Math.sin(lambda - lambda_0);
```

$t,\bar{t}$:

$$t = \sinh{\left(\tanh^{-1}\sin{\phi} - \frac{2\sqrt{n}}{1+n}\tanh^{-1}\left(\left[ \frac{2\sqrt{n}}{1+n}\sin{\phi}\right]\right)\right)}$$

$$\bar{t} = \sqrt{1+t^2}$$

```typsscript
const t = Math.sinh(Math.atanh(Math.sin(phi)) - (2*Math.sqrt(n) / (1+n)) * Math.atanh( (2*Math.sqrt(n) / (1+n)) * Math.sin(phi) ));

const bar_t = Math.sqrt(1 + t^2);
```

$\xi, \eta$:

$$\xi = \tan^{-1}\left(\frac{t}{\lambda_\epsilon}\right)$$

$$\eta = \tan^{-1}\left(\frac{\lambda_s}{t}\right)$$

```typescript
const xi = Math.atan(t / lambda_epsilon);

const eta = Math.atan(lambda_s / t);
```

$x,y$:

$$x = \bar{A} (\xi + \Sigma^5_{j=1} \alpha \sin{(2j\xi)} \cosh{(2j\eta)}) - \bar{S_{\phi_0}}$$

$$y = \bar{A} (\eta + \Sigma^5_{j=1} \alpha \sin{(2j\xi)} \cosh{(2j\eta)})$$

```typescript
const x = bar_A * (xi + alpha_List.map((alpha, j) => alpha * Math.sin(2*j*xi) * Math.cosh(2*j*eta) ).reduce((a, b) => a+b, 0)) - bar_S_phi_0;

const y = bar_A * (eta + alpha_List.map((alpha, j) => alpha * Math.sin(2*j*xi) * Math.cosh(2*j*eta) ).reduce((a, b) => a+b, 0));
```

#### まとめ


```typescript
interface GeoCoordinate {
  latitude: number;
  longitude: number;
};

interface PlaneCoordinate {
  x: number;
  y: number;
};

interface Configure {
  latitude_origin: number;
  longitude_origin: number;
  long_lad: number;
  oblateness: number;
  scale_factor: number;
};

const get_A_List:number[] = (n:number) => {
  return [
    1 + (n^2 / 4) + (n^3 / 64),
    -(3/2) * (n - ((n^3) / 8) - ((n^5) / 64)),
    (15/16) * (n^2 - ((n^4) / 4)),
    -(35/48) * (n^3 - ((5*n^5) / 16)),
    315/512 * n^4,
    693/1280 * n^5
  ];
};

const get_alpha_List:number[] = (n:number) => {
  return [
    (1/2)*n - (2/3)*(n^2) + (5/16)*(n^3) - (41/180)*(n^4) - (127/288)*(n^5),
    (13/48)*(n^2) - (3/5)*(n^3) + (557/1440)*(n^4) + (281/630)*(n^5),
    (61/240)*(n^3) - (103/140)*(n^4) + (15061/26880)*(n^5),
    (49561/161280)*(n^4) - (179/168)*(n^5),
    (34729/80640)*(n^5)
  ];
};

const get_bar_S_phi_0: number = (m_0: number, a:number, n:number, A_List:number[], phi_0:number) => {
  return (m_0*a / (1+n)) * (A_0*phi_0 + A_List.map((A, j) => A * Math.sin(2*j*phi_0) ).reduce((a, b) => a+b, 0));
};

const get_bar_A: number = (m_0:number, n:number, A_List:number[]) => {

  return (m_0 / (1+n)) * A_0;
};

const get_lambda_epsilon: number = (lambda:number, lambda_0:number) => {
  return Math.cos(lambda - lambda_0);
};

const get_lambda_s: number = (lambda:number, lambda_0:number) => {
  return Math.sin(lambda - lambda_0);
};

const get_t: number = (n:number, phi:number) => {
  return Math.sinh(Math.atanh(Math.sin(phi)) - (2*Math.sqrt(n) / (1+n)) * Math.atanh( (2*Math.sqrt(n) / (1+n)) * Math.sin(phi) ));
};

const get_bar_t: number = (t:number) => {
  return Math.sqrt(1 + t^2);
};

const get_xi: number = (t:number, lambda_epsilon:number) => {
  return Math.atan(t / lambda_epsilon);
};

const get_eta: number = (lambda_s:number, t:number) => {
  return Math.atan(lambda_s / t);
};


const GeoToPlane: PlaneCoordinate = (geo: GeoCoordinate, config: Configure) => {
  const latitude = geo.latitude;
  const longitude = geo.longitude;

  const latitude_origin:number = config.latitude_origin;
  const longitude_origin:number = config.longitude_origin;
  const long_lad:number = config.logn_lad;
  const oblateness:number = config.oblateness;
  const scale_factor:number = config.scale_factor;

  const n:number = = 1 / (2 * oblateness - 1);

  const A_List:number[] = get_A_List(n);
  const alpha_List:number[] = get_alpha_List(n);

  const bar_S_phi_0 = get_bar_S_phi_0(scale_factor, long_lad, n, A_List, latitude_origin);
  const bar_A = get_bar_A(scale_factor, n, A_List);

  const lambda_epsilon = get_lambda_epsilon(longitude, longitude_origin);
  const lambda_s = get_lambda_s(longitude, longitude_origin);
  const t = get_t(n, latitude);
  const bar_t = get_bar_t(t);
  const xi = get_xi(t, lambda_epsilon);
  const eta = get_eta(lambda_s, t);

  const x = bar_A * (xi + alpha_List.map((alpha, j) => alpha * Math.sin(2*j*xi) * Math.cosh(2*j*eta) ).reduce((a, b) => a+b, 0)) - bar_S_phi_0;
  const y = bar_A * (eta + alpha_List.map((alpha, j) => alpha * Math.sin(2*j*xi) * Math.cosh(2*j*eta) ).reduce((a, b) => a+b, 0));

  return {
      x: x,
      y: y,
  };
};
```
めんどい．


## 参考

1. [https://www.gsi.go.jp/common/000061216.pdf](https://www.gsi.go.jp/common/000061216.pdf)

2. [https://vldb.gsi.go.jp/sokuchi/surveycalc/surveycalc/algorithm/bl2xy/bl2xy.htm](https://vldb.gsi.go.jp/sokuchi/surveycalc/surveycalc/algorithm/bl2xy/bl2xy.htm)

3. [https://sw1227.hatenablog.com/entry/2018/11/30/200702](https://sw1227.hatenablog.com/entry/2018/11/30/200702)


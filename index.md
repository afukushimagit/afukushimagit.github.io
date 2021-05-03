# プログラムの実行(流れ)の制御
## フローチャート(流れ図)
プログラムの実行（手続き）の流れは，制御することができる．
これは，フローチャートによって図として表すことができる．

<img src="images/loop/flow_chart.png" alt="flow_chart" style="zoom:60%;" />

### 条件式
- プログラムの実行の流れを決める重要な要素，'判断'のパーツ．
<img src="images/loop/hishigata.png" alt="hishigata" style="zoom:60%;" />

ここで用いられるのが条件式．
- 式(Expression)の一種
- 要素
	- 値，変数
	- 比較演算子，論理演算子
- 計算結果（式が返す値）
	- 真(true)
	- 偽(false)
#### 条件式（比較演算子）
以下の条件を満たしたとき，式の結果は真(true)となり，
そうでなければ偽(false)となる．

|条件式|条件|
| ---- | ---- |
|`a < b`|aがbより小さい|
|`a <= b`|aよりb以下|
|`a > b`|aがbより大きい|
|`a == b`|aとbが等しい|
|`a != b`|aがbが等しくない|

## 繰り返し文
### できること

- 大量の図形を描画する
```java
size(400, 400);
 
for(int iIdx=0; iIdx<400; iIdx++)
{
circle( random(width), random(height), 20 );
}
```
<img src="images/loop/merit_01.png" alt="merit_01" style="zoom:80%;" />

- 大量のプロパティ(座標，サイズ，色など)を変更する

  ```java
  size(400, 400);
   
  for(int iIdx=0; iIdx<400; iIdx++)
  {
    fill( 170 + random(80), 100 + random(50), 200 );
    circle( random(width), random(height), 10 + random(15) );
  }
  ```

<img src="images/loop/merit_02.png" alt="merit_02" style="zoom:80%;" />

### for文
- 英語の前置詞for（〜の間）から由来．
- **指定した/回数**分．処理を繰り返す．
- あらかじめ繰り返す回数が決まっている場合に便利．

#### 書式
```java
for( 繰り返し用変数の宣言・初期化; 繰り返し条件式; 繰り返し用変数の更新式 )	 //セミコロン(；)無し
{
	(命令)文（複数書いてよい）;	// ←ここが繰り返される
    // インデント(字下げ)はエディタが自動で行ってくれる．
    // 手動で行う場合はTabキーを使う．
}
```

#### 処理の流れ

```java
for(int iIdx=0; iIdx<400; iIdx++)
{
  fill( 170 + random(80), 100 + random(50), 200 );
  circle( iIdx, random(height), 10 + random(15) );	// {}内で繰り返し用変数を使える.
}
```
1. 繰り返し用変数の宣言と初期化．

```java
int iIdx=0;
```

2. 繰り返し条件式の評価.

```java
iIdx<400	// 変数iIdxの値が400未満か
```
​	結果が '真' なら，手順3へ.
​	結果が '偽' なら，繰り返し終了し，{}の次の行に処理を移す.

3. {}内の(命令)文を実行.
```java
  fill( 170 + random(80), 100 + random(50), 200 );
  circle( iIdx, random(height), 10 + random(15) );
```
4.  繰り返し用変数の更新

```java
iIdx++		// 変数iIdxの値をインクリメント(1を足す)
```
​	手順2へ戻る.

#### 例：星空を描く
```java
size(400, 400);
colorMode( HSB, 360, 1.0, 1.0, 1.0 ); // HSBA
background( 220, 0.1, 0.1 );          // 背景色
noStroke();                           // 線無し
 
for(int iIdx=0; iIdx<400; iIdx++)     // 400回繰り返す.
{
  fill( random(360),          // H
        0.1 + random(0.4),    // S
        1.0,                  // B
        0.2 + random(0.8) );  // A
        
  circle( random(width), random(height), 1 + random(3) );
}
```
<img src="images/loop/hoshizora.png" alt="hoshizora" style="zoom:80%;" />
アレンジしてみましょう．

- 星の数を増やす.
- 色や明るさ，大きさを変える

### for中における変数の使用
繰り返し用変数は{}の中でも使うことができる．
```java
// グラデーションの対角線を描画.
colorMode(RGB, 100);
for(int iX=0; iX<100; iX++)
{
  stroke(iX, iX, iX);
  point(iX, iX);
}
```
<img src="images/loop/var_in_loop.png" alt="var_in_loop" style="zoom:100%;" />

### forのネスティング
forの中で，さらにforを使うことができる．
これは何段階でも使用でき，複雑な繰り返し処理が可能となる．
```java
// グラデーションのカラーチャート.
size(200, 200);
colorMode(HSB, 100);
background(99);
 
for(int iY=0; iY<10; iY++)        // Y軸方向への処理
{
  for(int iX=0; iX<10; iX++)      // X軸方向への処理
  {
    fill( iX*10, 10+iY*10, 99 );
    rect( iX*20, iY*20, 10, 10 );
  }
}
```
<img src="images/loop/nesting.png" alt="nesting" style="zoom:100%;" />

### while文

- 英語の接続詞while（〜の間ずっと）から由来．

- 指定した条件満たす限り，処理を繰り返す．
- 何かの数を数え上げる場合などに便利．

#### 書式
```java
while (繰り返し条件式)
{
	繰り返したい命令;
}
```
#### 例
```java
size( 400, 200 );
 
int iCount = 0;
 
while( 40*iCount < width )  // スクリーン右端まで処理を続ける
{
  rect( 40*iCount, 80, 20, 20 );
  
  iCount++;  // iCountのインクリメント.
}
 
print( "描画した矩形の数は" + iCount + "個" );
 
// コンソール表示：描画した矩形の数は10個
```

**本授業では基本的にforを使うことを推奨**


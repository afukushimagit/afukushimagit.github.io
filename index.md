
# 繰り返し文
## できること

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

## for文とwhile文の違い

### for文

- 英語の前置詞for（〜の間）から由来．
- **指定した回数**分．処理を繰り返す．
- あらかじめ繰り返す回数が決まっている場合に便利．

### while文

- 英語の接続詞while（〜の間ずっと）から由来．

- 指定した条件満たす限り，処理を繰り返す．
- 繰り返す回数は決まっていない場合に便利．

## for文

```java
for( 繰り返し用変数の宣言・初期化; 繰り返し条件式; 繰り返し用変数の更新式 )	 //セミコロン(；)無し
{
	(命令)文（複数書いてよい）;	// ←ここが繰り返される
    // インデント(字下げ)はエディタが自動で行ってくれる．
    // 手動で行う場合はTabキーを使う．
}
```

```java
for(int iIdx=0; iIdx<400; iIdx++)
{
  fill( 170 + random(80), 100 + random(50), 200 );
  circle( random(width), random(height), 10 + random(15) );
}
```


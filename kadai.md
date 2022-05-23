# 課題3 時計のデザイン

新しく斬新な時刻の表示方法をデザインし，それをプログラムで表現しなさい．

## 目的

変数を使ったアニメーションの記述方法に慣れること．
アプリケーション開発の基礎を体験する．

## 条件

- 画面の大きさ・縦横比ともに自由とするが，最大でも1000×1000ピクセル以内に収めること．
- 現在時刻の時間，分，秒の値がビジュアルに影響を与えること．
- 視認性は問わず，どのように時計を読み取るのか一見して分かりにくい表現でも可とする．
- ただし，あくまで時計のデザインなので，時間の関数を使えば何でもいいわけではない．
- 装飾に終始しないこと．単なるアナログ時計風の画像やデジタル時計風の文字が表示されるだけのプログラムは評価の対象とならない．
- 文字や数字を用いて良いが，単に時刻が数字・文字等で表示されるだけの時計は不可とする．時間情報と組み合わせて表示形式に必ず何らかの工夫を凝らすこと．

## 提出までの流れ

1. 提出期限：**6/13(月) 23:59**

2. Processingを使用して，pde ファイルを作成，作品を作る．

   - フォルダおよびpdeファイルの名前は，**k3\_クラス+クラス番号\_苗字**
   - 例：k3_A02_akama,   k3_B16_shibata,  k3_C22_saitou

3. プログラムの最上段に**作品の簡単な解説**を入れる．
   以下のように，コメントとして記入してください．

   ```java
   /**
   
   ランダムな点描によるデジタル時計
   分や時が切り替わった後，徐々に描画が変化していく．
   
   */
   
   void setup()
   {
     
     
   }
   
   void draw()
   {
     
     
   }
   ```

4. 作品が完成したら，スケッチブック(pdeが入ったフォルダ)を**zip形式に圧縮**する.

   k3\_クラス+クラス番号\_苗字.zip が出来上がるはず．

5. ファイルの提出 Teams課題３のページで「+作業の追加」をクリックして，出来上がった以下のファイルをアップロードしてください．

   - **k3\_クラス+クラス番号_苗字.zip**

      ※課題３ではパワーポイントファイルの提出はありません．


## 評価

- アニメーションが時刻の変化に十分な影響を受けていると評価が高くなる．
  - 時刻と関係ないにぎやかしのようなアニメーションはあまり加点されない．

- 秒単位で見た目の変化がわかるものは評価が高くなる．
  - 分，時間が変わるタイミングのみでの変化は講評がしにい．
- 適切にコメントを記述しているか．

## 注意事項

- 工夫が全くなく，単に時刻を数字で表示するだけのプログラムについては０点とする．
- 類似作品が複数見つかった場合は，盗作の恐れがあるとみなして厳しく調査する．
- ネット上のサンプルや参考書籍のプログラムを応用する場合は，出典を何らかの形式で明記し，どのように応用しているのか説明すること．盗作が疑われる場合は厳しく対処する．
- 第６回時点で本科目で教えていない技術を使用しないこと．インタラクション，サウンド，外部ライブラリ等の使用は不可とする．課題の主目的は表現であり，過度に技術に注力することは避けること．
- メインは第７回授業だが，**関数の作成，使用は可**．
  - できれば積極的に使用しましょう．
- 課題として，何が求められているかを理解して制作すること．
- スケッチ（.pdeファイル）は一つのみとする．ソースファイルを分割・複数に分割しないこと．

 

## 参考作品

※ページ軽量化のため，各作品のGIFは解像度を落としてあります．

### sample01

講師（福嶋）制作
 ※難しいです．

```java
/**

ランダムな点描によるデジタル時計
分や時が切り替わった後，徐々に描画が変化していく．

*/

// 時間表示のスケール値[（スクリーンの）幅に対する率,高さに対する率]
final float DIGITS_SCALE[] = { 0.80, 0.60 };
 
// 各桁間の間隔 一桁分の幅に対する率
final float DIGIT_INTERVAL = 0.6;
// コロンの幅 一桁分の幅に対する率
final float COLON_WIDTH_RATE = 0.1;
 
// 桁番号 01:23
// 各桁の座標[桁番号][x,y]
float fDigitPos[][] = new float[4][2];
 
// 各桁のサイズ[桁番号][w,h]
float fDigitSize[][] = new float[4][2];
 
// コロンの挿入場所(桁番号に対応)
final int COLON_LOC_IDX = 2;
 
/** セグメント番号
     4
     -
   0|5|2
     -
   1| |3
     -
     6
*/
// セグメント範囲の左上座標 [セグメント番号][x,y]
float fSegmentUpperLeft[][] = new float[7][2];
// セグメント範囲のサイズ [セグメント番号][w,h]
float fSegmentSize[][] = new float[7][2];
// セグメント同士の重なり [0-1]
float fSegmentsLap = 0.5;
// セグメントの太さ [0-1]
float fSegmentsThickness[] = { 0.2, 0.1 };
 
// 数に対して，表示を行うかのフラグ[標示数値] [セグメント番号]
final boolean[][] DIGIT_DISP_FLG ={{true, true, true, true, true, false,true},   //0
                                   {false,false,true, true, false,false,false},  //1
                                   {false,true, true, false,true, true, true},   //2
                                   {false,false,true, true, true, true, true},   //3
                                   {true, false,true, true, false,true, false},  //4
                                   {true, false,false,true, true, true, true},   //5
                                   {true, true, false,true, true, true, true},   //6
                                   {true, false,true, true, true, false,false},  //7
                                   {true, true, true, true, true, true, true},   //8
                                   {true, false,true, true, true, true, false} };//9
 
 
void setup()
{
  // スクリーンサイズ(※手入力)
  size( 800, 450 );
  
  int iDigitsCount = fDigitPos.length;  // 桁の数
  
  // 各桁の座標と幅
  float fDigitsWidth = width * DIGITS_SCALE[0];
  float fDigitWidth = fDigitsWidth / (iDigitsCount*(1+DIGIT_INTERVAL)+COLON_WIDTH_RATE);
  float fDigitHeight = height * DIGITS_SCALE[1];
  float fDigitInterval = fDigitWidth * DIGIT_INTERVAL;  
  float fDigitPosX = ( width - fDigitsWidth ) / 2.0;
  for(int iDigitIdx = 0; iDigitIdx < fDigitPos.length; iDigitIdx++)
  {
    // コロンの場所を挿入.
    if( iDigitIdx == COLON_LOC_IDX)
    {
      fDigitPosX += fDigitWidth*COLON_WIDTH_RATE + fDigitInterval;
    }
    fDigitPos[iDigitIdx][0] = fDigitPosX;
    fDigitPos[iDigitIdx][1] = ( height - fDigitHeight ) / 2.0;
    fDigitSize[iDigitIdx][0] = fDigitWidth;
    fDigitSize[iDigitIdx][1] = fDigitHeight;
    fDigitPosX += fDigitWidth + fDigitInterval;
  }
  
  // 縦長と横長のセグメントのサイズ[w,h]
  float fSegmentSizeV[]  = new float[2]; 
  float fSegmentSizeH[]  = new float[2];
  fSegmentSizeV[0] = fSegmentsThickness[0] * fDigitWidth;
  fSegmentSizeH[1] = fSegmentsThickness[1] * fDigitHeight;
  fSegmentSizeV[1] = ( fDigitHeight - (1-fSegmentsLap)*(3 * fSegmentSizeH[1]) ) / 2.0;
  fSegmentSizeH[0] = fDigitWidth - (1-fSegmentsLap)* (2 * fSegmentSizeV[0]);
  
  // 各セグメントのローカル座標
  float fSegmentOfs[] = { ( fDigitWidth - fSegmentSizeH[0] )    / 2.0,
                          ( fDigitHeight - 2*fSegmentSizeV[1] ) / 3.0 };
  fSegmentUpperLeft[0][0] = 0.0;
  fSegmentUpperLeft[1][0] = 0.0;
  fSegmentUpperLeft[2][0] = fDigitWidth - fSegmentSizeV[0];
  fSegmentUpperLeft[3][0] = fSegmentUpperLeft[2][0];
  fSegmentUpperLeft[4][0] = fSegmentOfs[0];
  fSegmentUpperLeft[5][0] = fSegmentOfs[0];
  fSegmentUpperLeft[6][0] = fSegmentOfs[0];
  fSegmentUpperLeft[4][1] = 0;
  fSegmentUpperLeft[0][1] = fSegmentOfs[1];
  fSegmentUpperLeft[2][1] = fSegmentOfs[1];
  fSegmentUpperLeft[5][1] = fDigitHeight / 2.0 - fSegmentSizeH[1] / 2.0;
  {
    float fOfsY = fSegmentSizeV[1] + 2 * fSegmentOfs[1];
    fSegmentUpperLeft[1][1] = fOfsY;
    fSegmentUpperLeft[3][1] = fOfsY;
  }
  fSegmentUpperLeft[6][1] = fDigitHeight - fSegmentSizeH[1];
  for(int iSegmentIdx = 0; iSegmentIdx <= 3; iSegmentIdx++)
  {
    fSegmentSize[iSegmentIdx][0] = fSegmentSizeV[0];
    fSegmentSize[iSegmentIdx][1] = fSegmentSizeV[1];
  }
  for(int iSegmentIdx = 4; iSegmentIdx < fSegmentUpperLeft.length; iSegmentIdx++)
  {
    fSegmentSize[iSegmentIdx][0] = fSegmentSizeH[0];
    fSegmentSize[iSegmentIdx][1] = fSegmentSizeH[1];
  }
  
  background(50,50,50);
  colorMode(RGB,256);
  frameRate(12);
}
 
void draw()
{
/*
  // test:各桁のマスク表示
  for(int iDigitIdx = 0; iDigitIdx < fDigitPos.length; iDigitIdx++)
  {
    noStroke();
    fill(255,255,255);
    rect( fDigitPos[iDigitIdx][0],fDigitPos[iDigitIdx][1],
          fDigitSize[iDigitIdx][0],fDigitSize[iDigitIdx][1]);
  }
*/
/*
  // test:セグメントマスク表示
  for(int iDigitIdx = 0; iDigitIdx < fDigitPos.length; iDigitIdx++)
  {
    noStroke();
    fill(100,100,100);
    for(int iSegmentIdx = 0; iSegmentIdx < fSegmentUpperLeft.length; iSegmentIdx++)
    {
      rect( fSegmentUpperLeft[iSegmentIdx][0]+fDigitPos[iDigitIdx][0],
            fSegmentUpperLeft[iSegmentIdx][1]+fDigitPos[iDigitIdx][1],
            fSegmentSize[iSegmentIdx][0],
            fSegmentSize[iSegmentIdx][1] );
    }
  }
*/
/*
  // test:現在時間マスク
  int iHour = hour() ;
  int iMinute = minute();
  int iDispNumbers[] = { iHour/10, iHour%10, iMinute/10, iMinute%10 };
  noStroke();
  for(int iDigitIdx = 0; iDigitIdx < fDigitPos.length; iDigitIdx++)
  {
    int iDispNumber = iDispNumbers[iDigitIdx];
    for(int iSegmentIdx = 0; iSegmentIdx < fSegmentUpperLeft.length; iSegmentIdx++)
    {
      if( DIGIT_DISP_FLG[iDispNumber][iSegmentIdx] )
      {
        fill(255,0,0);
        rect( fSegmentUpperLeft[iSegmentIdx][0]+fDigitPos[iDigitIdx][0],
              fSegmentUpperLeft[iSegmentIdx][1]+fDigitPos[iDigitIdx][1],
              fSegmentSize[iSegmentIdx][0],
              fSegmentSize[iSegmentIdx][1] );
      }
    }
  }
*/
 
  // 点描レンダリング
  int iHour = hour() ;
  int iMinute = minute();
  int iDispNumbers[] = { iHour/10, iHour%10, iMinute/10, iMinute%10 };
  noStroke();
  for(int iDigitIdx = 0; iDigitIdx < fDigitPos.length; iDigitIdx++)
  {
    int iDispNumber = iDispNumbers[iDigitIdx];
    for(int iSegmentIdx = 0; iSegmentIdx < fSegmentUpperLeft.length; iSegmentIdx++)
    {
      if( DIGIT_DISP_FLG[iDispNumber][iSegmentIdx] )
      {
        fill(random(255),random(255),random(255),50);  // 点描
      }
      else
      {
          fill(50,50,50,50);                          //　徐々にフェードアウト
      }
      circle( fSegmentUpperLeft[iSegmentIdx][0]+fDigitPos[iDigitIdx][0] + random(fSegmentSize[iSegmentIdx][0]),
              fSegmentUpperLeft[iSegmentIdx][1]+fDigitPos[iDigitIdx][1] + random(fSegmentSize[iSegmentIdx][1]),
              6+random(10))  ;
    }      
  }

}
```

![kadai03_fukushima_01](images/kadai3/kadai03_fukushima_01.gif)

------

### sample02

講師（福嶋）制作

```java
/**
 コサイン波時計
 
 時：波の数(1~23個)
 分：波線の太さ(1~15pixel)
 秒：波の高さ(0~スクリーンの高さまで)
 
*/
// 調整用パラメータ
int iFPS = 30;         // fps
 
int iFrameIdx;         // フレーム番号:0~iFPS
  
void setup()
{
  size( 800,400 );
  colorMode( HSB, 360, 1.0, 1.0, 1.0 );
  frameRate(iFPS);
  
  iFrameIdx = 0;      // グローバル変数初期化
}
void draw()
{
  // スクリーンリセット.
  noStroke();
  fill( 0, 0, 0.4 );
  rect( 0, 0, width, height );
  
  int iHour = hour();
  int iMinute = minute();
  
  float fAmplitude = (height/2) * second() / 60.0;     // 振幅
  float fFrequency = 1 + 12*(60*iHour+iMinute)/1440.0; // 振動数
  float fAddDegPerX = 360*fFrequency / width;        // X座標値1進んだ時の角度の増加
  
  int iAnimWaveDeg = 360 * iFrameIdx / iFPS;    // アニメーション用の波の角度
 
 
  // コサイン波形
  float fStrokeWeight = 1+14*(iMinute*60+second())/3600.0;
  strokeWeight( fStrokeWeight );
  for( int iX=0; iX < width; iX++ )
  {
    float fAngle0 = iX*fAddDegPerX+iAnimWaveDeg;        // 波の角度
    float fAngle1 = (iX+1)*fAddDegPerX+iAnimWaveDeg;    // 次のピクセルの波の角度
     
    // 一つの波で色相360°
    stroke( (iX*fAddDegPerX)%360, 1.0, 1.0 );
    
    line( iX, height/2 + fAmplitude*cos( radians(fAngle0) ),
          iX, height/2 + fAmplitude*cos( radians(fAngle1) ) );
  }
  
  // 左に進むアニメ用変数更新.
  if( iFrameIdx >= iFPS )
  {
    iFrameIdx = 0;
  }
  else
  {
    iFrameIdx++;
  }
}
```

![kadai03_fukushima_02](images/kadai3/kadai03_fukushima_02.gif)

------

### sample03

講師（福嶋）制作

```java
/**
 シェルピンスキーのギャスケットを使った時計
 
 時間，分，秒のそれぞれ初期ドットを一番上に打つ.
 
*/
int[][] cell=new int[600][300];       // スクリーンサイズ分の配列.
int iFPS = 4;         // fps
 
int iFrameIdx;         // フレーム番号:0~iFPS
 
 
void setup()
{
  size(600, 300);
  colorMode( HSB, 6, 1, 1 );
  frameRate( iFPS );
  
  // 配列の初期化
  for(int iX=0; iX<cell.length; iX++)
  {
    for(int iY=0; iY<cell[iX].length; iY++) 
    {
      cell[iX][iY]=0;
    }
  }
  iFrameIdx = 0;
}
 
void draw()
{
  fill(0,0,0.5);
  noStroke();
  rect(0, 0, width, height);  // スクリーンリフレッシュ
  
  
  int iSecond = second();
  int iDrawXSec = floor( width * ( iSecond / 60.0 ) );

  int iMinute = minute();
  int iDrawXMin = floor( width * ( iMinute / 60.0 ) );
  
  int iHour = hour();
  int iDrawXHour = floor( width * ( iHour / 24.0 ) );
  
  int iDrawXFrame = floor( width * ( iFrameIdx / float(iFPS) ) );

  // 初期状態：
  for(int iX=0; iX<width; iX++)
  {
    if( iX==iDrawXSec )
    {
      cell[iX][0]= 1;
    }
    else if( iX==iDrawXMin )
    {
      cell[iX][0]= 3;
    }
    else if( iX==iDrawXHour )
    {
      cell[iX][0]= 5;
    }
    else if( iX==iDrawXFrame )
    {
      cell[iX][0]= 0;
    }
    else
    {
      cell[iX][0]= 0;
    }
  }
  
  for(int iY=1; iY<height; iY++)  // ２行目から繰り返し開始．
  {
    for(int iX=0; iX<width; iX++)
    {
      if( iX == 0 )             // スクリーン左端の処理.
      {
        cell[iX][iY] = ( 0 + cell[iX+1][iY-1] )%6;
      }
      else if( iX == width-1 )  // スクリーン右端の処理.
      {
        cell[iX][iY] = ( cell[iX-1][iY-1] + 0 )%6;
      }
      else
      {
        // 隣接するセルの合計.
        cell[iX][iY] = ( cell[iX-1][iY-1] + cell[iX+1][iY-1] )%6;
      }
      
      if( cell[iX][iY] > 0 )    // もしもセルの値が0以上なら.
      {
       
       if( cell[iX][iY]==5 )
        {
          stroke( 0, 0, 1 );
        }
        else if( cell[iX][iY]==1 )
        {
          stroke( 0, 0, 1 );
        }
        stroke( 0, 0, 1 );
        point( iX, iY );
      }
      else
      {
        stroke( 0, 0, (iX/float(width))*(iY/float(height))-0.2 );
        point( iX, iY );
      }
    }
  }
  
  // フレームアニメ用変数更新.
  if( iFrameIdx >= iFPS )
  {
    iFrameIdx = 0;
  }
  else
  {
    iFrameIdx++;
  }
}
```

![kadai03_fukushima_03](images/kadai3/kadai03_fukushima_03.gif)

------

### sample04

```java
//時間の抽象化
//大体の時間を知るための時計。
//視認性をギリギリにするために、背景に線を入れる。
 
void setup()
{
  size(940,380);
  colorMode(RGB,256);
}
 
void draw() 
{
  background(20,40,48);

  //円は２４分割が均等ではなかったため、３時、６時、９時、１２時を太線で表す。  
  stroke(32,64,77);
  strokeWeight(3);
  line(0,273,330,273);//3 
  line(0,257.5,330,257.5);//6
  line(0,232,330,232); //9
  line(0,197.5,330,197.5);//12
  line(0,167.5,330,167.5);//15
  line(0,137.5,330,137.5);//18 
  line(0,115,330,115);//21 
  line(0,100,330,100);//２４
  
  for(int i=0;i<=100;i+=30)//円（時間）の後ろの線①
  {
    stroke(32,64,77); 
    strokeWeight(1);
    line(0,10+i,330,10+i);
  }
  
  for(int i=0;i<=280;i+=30)//円（時間）の後ろの線②
  {
    stroke(32,64,77); 
    strokeWeight(1);
    line(0,280+i,330,280+i);
  }
  
  //秒・分は１０秒・分単位の線で表現 
  for(int i=0;i<380;i+=30)//三角と四角の（秒・分の裏） 
  {
    stroke(32,64,77);
    strokeWeight(1);
    line(330,10+i,940,10+i);
  }

   //ここから時計本体の事 
   //時間の事
  int h = hour();
  int m = minute();
  int s = second();
  println(h+":"+m+":"+s);

  //後ろの影
  fill(32,64,77);
  noStroke();
  triangle(470, 100, 380, 280, 560, 280);
  rect(660, 100,180, 180);
  
  fill(255,211,66);
  ellipse(190, 190, 180, 180);
  
  //時間
  fill(32,64,77);
  arc(190,190,180,180,radians((-270)+180/24*h),radians(90-180/24*h),CHORD);
  //分
  fill(24,217,178);
  triangle(470, 280-(180/60*m), 380, 280, 560, 280);
   //秒
  fill(237,40,80);
  rect(660,280,180,-s*(180/60));    
}
```

![kadai03_02](images/kadai3/kadai03_02.gif)

------

### sample05

```java
   void setup()
  {
    size(650,650);
    colorMode(RGB,256);
    noFill();
    //RGB256段階、線のみ
  }
  
  void draw()
  {
    background(0);
    //背景塗りつぶし    
   
    float i = millis()/10;
    float s = second();
    float m = minute();
    float h = hour();
    //1/100秒、１秒、１分、１時間での変化を関数に設定
  
      for(int iX=0; iX<100; iX++)//円を１００回描画
      {
        stroke(iX*2,255-4*m,255);
        //分が進むにつれより赤くなる、一時間たつと青に戻る。描画ごとにR値が上がりグラデーション変化
        strokeWeight(1);
        ellipse(cos(radians((iX+i)*27))*100+width/2,sin(radians((iX+i)*27))*100+height/2,150+iX+h*2,150+iX-h*2);
        //回数を重ねるごとに大きくなる円を円状に１００回描画、時間が進むと横につぶれた楕円になる。1/100秒で動きをつける
       
        strokeWeight(0.3);
        ellipse(cos(radians(s*6))*100+width/2,sin(radians((s*6)))*100+height/2,250+h*2,250-h*2);
        //上記の大きさの変化の中で一番大きな円の座標が秒ごとに回転、一周で６０秒
      
      }
  }
```

![kadai03_03](images/kadai3/kadai03_03.gif)

------

### sample06

```java
void setup()
{
    size(700, 600);
    background(255);
    smooth();
    frameRate(30);
}
 
 
 
void draw()
 {  
    translate(width/2, height/2); // 原点を画面の中心に移動
    
    noStroke();//画面をリセット
    fill(255,50);
    rect(-350,-300,700,600); 
    
    float s = second();//秒数を取得
    float m = minute();//分
    float h = hour();//時間
         
              
    
   //秒針の動きに対応する花弁１を描画する    
    pushMatrix();//秒針の保存
    
    rotate(radians(s*(360/60)));//秒針が動く角度
      
    for(int iX=0; iX<500; iX+=5)//楕円を角度ごとに繰り返して花弁を描写
       {  
         rotate(radians(20));
         stroke(255,50);
         strokeWeight(2);  
         fill(0,10);
         ellipse(5,150,60,200); 
       }
          
     popMatrix();//秒針の復元
         
    
 
    //分針に対応する花弁２ 
     pushMatrix();
     
     noStroke();
     for(int iX=0; iX<500; iX+=5)  
        {         
          rotate(radians(20));
          stroke(0,40);
          strokeWeight(2);        
          fill(iX,30);
          ellipse(5,150,60,200);       
        }
        
      popMatrix();
     
     
     
    //時針に対応する花弁3  
      pushMatrix();     
      
      for(int iX=0; iX<500; iX+=5)   
        {         
          rotate(radians(20));
          stroke(0,50);
          fill(0,5);
          ellipse(5,50,50,200);                  
        }
      
    //中心のピンク    
      fill(255,0,255);
      ellipse(0,0,50,50);
      
      popMatrix();



    //それぞれの針に対応するオブジェクトを追加で描画する
    //時針に対応するてんとう虫     
      pushMatrix();
     
      scale(0.5);//大きさを縮小
      rotate(radians(h*(360/12)));
      translate(-2, -80);//てんとう虫の中心を時針が通過するよう移動
      rotate(radians(90));//時計回りの方向を向くように回転
       
      stroke(0);
      strokeWeight(5.5);
      fill(255,0,255);
      ellipse(0,0,60,60);//羽部分
      fill(0);//あたま
      ellipse(0,-25,30,13);   
      ellipse(0,3,9,9);//斑点
      ellipse(10,15,9,9);
      ellipse(-10,15,9,9);
      ellipse(-15,-2,9,9);
      ellipse(15,-2,9,9);    
      fill(255);//目玉
      ellipse(7,-25,10,10);
      ellipse(-7,-25,10,10);
      
      popMatrix();
      
      
      
    //分対応ちょうちょ     
      pushMatrix();
        
      rotate(radians(m*(360/60)));//分針が動く角度
      translate(25, -120);//ちょうちょを移動
      rotate(radians(90));//時計回り方向を向くように回転
      
      stroke(0);
      strokeWeight(3);
      fill(255);
      ellipse(-25,20,60,60);//羽
      ellipse(25,20,60,60);  
      ellipse(-20,55,30,30);
      ellipse(20,55,30,30);      
      fill(0);//胴体   
      ellipse(0,20,10,30);  
      stroke(0);//触角
      strokeWeight(2);
      line(20,-20,0,0);
      line(-20,-20,0,0);
      
      popMatrix();
    
    
     
    //秒対応ちょうちょ   
      pushMatrix();
    
      scale(1.5);
      rotate(radians(s*(360/60)));//秒針が動く角度
      translate(20, -130);//ちょうちょを移動
      rotate(radians(90));//時計回り方向にに向くように回転
 
      stroke(0);
      strokeWeight(2);
      fill(255);
      ellipse(-25,20,60,60);//羽
      ellipse(25,20,60,60);  
      ellipse(-20,55,30,30);
      ellipse(20,55,30,30); 
      ellipse(-33,70,15,15);
      ellipse(33,70,15,15);      
      fill(0);//胴体   
      ellipse(0,20,10,30);  
      stroke(0);//触角
      strokeWeight(1.5);
      line(20,-20,0,0);
      line(-20,-20,0,0);
      
      popMatrix();
}          
         
 
//参考にしたサイト「yoppa org – Processingで時計を作る」　https://yoppa.org/proga10/1419.html
//radiansによる針の角度の算出、pushMatrix,popMatrixによる針の保存と復元
```

![kadai03_04](images/kadai3/kadai03_04.gif)

------

### sample07

```java
void setup()
{
  size(500,500);
  colorMode(RGB);
  noStroke();
  frameRate(30);
}
 
void  draw()
{
  
  background(255,120,100);
  int d = second();
  int m = minute();
  int y = hour();
  
  
  //午後
  fill(53,62,87);
  rect(0,250,500,250);
  
  
  //秒：鳥（画面を横にに60分割）
  fill(53,62,87);
  ellipse(d*8.3-50,50,80,80);
  ellipse(d*8.3,50,25,5);
  ellipse(d*8.3-12,50,30,20);
  fill(255,120,100);
  ellipse(d*8.3-80,50,80,80);
  fill(53,62,87);
  ellipse(d*8.3-40,50,50,20);
  fill(255,120,100);
  ellipse(d*8.3-60,50,25,5);
  
  
  //時間：太陽（画面を縦に２４分割、午前午後で色が変わる）
  if(y*20>250)
  {
   fill(255,120,100);
  }
  else
  {
   fill(53,62,87);
  }
  ellipse(250,y*20.8,80,80);
  
  
  //分：ヨット（画面を横にに60分割）
  fill(53,62,87);
  triangle(m*8.3-40,235,m*8.3,235,m*8.3,150);
  triangle(m*8.3+5,235,m*8.3+35,235,m*8.3+5,160);
  triangle(m*8.3-45,240,m*8.3,270,m*8.3+40,240);
 
}
```

![kadai03_05](images/kadai3/kadai03_05.gif)

------

### sample08

```java
void setup()
 
{
  size(600,600);
  frameRate(8);
  smooth();
  colorMode(RGB,256);
  background(255,255,255);
  noStroke();
}
 
 
void draw()
 
{
  float h =hour()% 12 ;
  float m =minute() ;
  float s =second();
  float arcStart=PI*1.5;
 
  //時針
  fill  (250,0,0,13);
  arc(300,300,420,420,arcStart,arcStart+radians(h*30));
   
  //分針
  fill  (100,0,150,10);
  arc(300,300,480,480,arcStart,arcStart+radians(m*6));
  
  //秒針
  fill  (0,random(50,255),random(50,255),10);
  arc(300,300,540,540,arcStart,arcStart+radians(s*6));
  
  //白背景
  fill(255,255,255,10);
  rect(0,0,600,600);
}
```

![kadai03_06](images/kadai3/kadai03_06.gif)

------

### sample09

```java
//運河などの水位調節に用いられる閘門をモチーフに制作しました。
 
void setup()
{
   size(650,500);
   frameRate(3);
   colorMode(RGB,256);
  
}
   
  
 
void draw()
{
  
 
  //背景色---------------------------------------------------------------------  
   
   
   if(18<h||h<4)    //背景色(夜)
   { 
        background(81,88,107);
       
    }
   
    else if(4<h&&h<6||17<h&&h<19) //背景色（宵、暁）
    {
        background(229,163,122);
        
    }
    
   else                          //背景色（朝昼）
    {
      background(146,210,209);
     }
     
//山----------------------------------------------------------------------------
     
   noStroke();
    fill(156,153,132,60);
   triangle(0,420,80,100,700,500);
   
    fill(156,153,132,20);
   triangle(0,470,260,140,665,400);
   
   
   //水門--------------------------------------------------------------------
   
   noStroke();          //第一水門
   fill(130,130,130);
  if(m<=20)
  {
     rect(50,415-iGatehight1,10,100+iGatehight1);
  }
 
  else
  {
       rect(50,415,10,500);
  }
   
   noStroke();          //第二水門
   fill(130,130,130);
   
  if(21<m&&m<=40)
   {
     rect(230,275-iGatehight2,10,500);
    }
   
  else
      {
           rect(230,275,10,225);
      }
   
       
   noStroke();          //第三水門
   fill(130,130,130);
   rect(410,50,10,450);
   
   noStroke();          //第四水門
   fill(130,130,130);
   rect(590,50,10,450);
   
  //for(int iwater=0;iwater<160;iwater++)  //左水
  {
      //float fRad= random(10); 
      //fill(64,random(134,221),random(178,207),random(50,60));
     // rect(random(0,45),random(425,500),8,8);
  }
 
 
//水域---------------------------------------------------------------------------------
 
 
 water1(0,52,425,500);            //左淵　水域
 
 if(m<=20)                                    //第一門〜第二門　水域
 {
     water1(70,233,411-iGatehight1,500);      
 }
 else
 {
     water1(70,233,411,500);  
 }
 
if(m<21||m>=41)                //第二水門〜第三水門 水域
water2(250,410,275,500);   
else
water2(250,410,275-iGatehight2,500);  
 
     
 
 water3(428,593,50,500);      //第三門〜第四門　　水域
 water2(610,650,50,500);      //右淵　水域
 
 //船---------------------------------------------------------------------------------
 
 noStroke();
 fill(80,80,80);
 
if(m<=20)
 {
     rect(50+iGatelength,410-iGatehight1,25,7);
     fill(230,230,230);
     triangle(74+iGatelength,408-iGatehight1,
                    55+iGatelength,408-iGatehight1,
                    55+iGatelength,390-iGatehight1
                   );
                    
     ;
 }
else if(m<40)
 {
     fill(80,80,80);
     rect(50+iGatelength,270-iGatehight2,25,7);
     fill(230,230,230);
     triangle(74+iGatelength,268-iGatehight2,
                   55+iGatelength,268-iGatehight2,
                   55+iGatelength,250-iGatehight2
                  );
 }
 else
 {
     fill(80,80,80);
     rect(50+iGatelength,50,25,7);
     fill(180,180,180);
     triangle(74+iGatelength,48,
                   55+iGatelength,48,
                   55+iGatelength,30
                  );
 }
 
 
 //標示-------------------------------------------------------------------------------
 
fill(200);
textSize(12);
text("0", 51, 430);
textSize(10);
text("m", 50, 440);
 
 fill(180,0,0);
textSize(12);
text("2", 231, 288);
fill(200);
textSize(12);
text("0", 231, 298);
textSize(10);
text("m", 230, 306);
 
 
 
 fill(200);
textSize(12);
text("3", 412, 145);
textSize(12);
text("0", 412, 155);
textSize(10);
text("m", 411, 163);
 
 fill(189,0,0);
textSize(12);
text("4", 412, 65);
fill(200);
textSize(12);
text("0", 412, 75);
textSize(10);
text("m", 411, 83);
 
fill(180,0,0);
textSize(12);
text("6", 592, 65);
fill(200);
textSize(12);
text("0", 592, 75);
textSize(10);
text("m", 591, 83);
 
 ellipse(140,60,25,25);
 fill(170);
 textSize(16);
 text(h, 131, 65);      
}
 
//ここから関数----------------------------------------------------------------------
 
void water1(int ix1,int ix2,int iy1,int iy2)//水の関数 密度低
{
  for(int iwater=0;iwater<500;iwater++) 
    
    {
    
      fill(64,random(134,221),random(178,207),random(50,60));
       rect(random(ix1-10,ix2-10),random(iy1,iy2),10,10);
  }
}
 
 
void water2(float ix1,float ix2,float iy1,float iy2)//水の関数　密度高
{
  for(float iwater=0;iwater<1000;iwater++) 
   
    {
     
      fill(64,random(134,221),random(178,207),random(50,60));
      rect(random(ix1-10,ix2-10),random(iy1,iy2),10,10);
    }
}
 
void water3(int ix1,int ix2,int iy1,int iy2)//水の関数　密度最高
{
  for(int iwater=0;iwater<2000;iwater++) 
   
    {
     
      fill(64,random(134,221),random(178,207),random(50,60));
      rect(random(ix1-10,ix2-10),random(iy1,iy2),10,10);
    }
}
 
   int s = second(); //秒
    int m =minute();//分
    int h = hour();//時
    
   
   int iGatehight1=m*7;  //増減量
   int iGatehight2=m*11/2;
   int iGatehight3=m-50*7;
   int iy=410-iGatehight1;
   int iGatelength=m*9;
```

![kadai03_07](images/kadai3/kadai03_07.gif)

------

### sample10

※講師作 ３パターンのフェードする矩形

```
int iFPS = 20;
 
int iCurrentS;
int iCurrentM;
int iCurrentH;
int iFadeInFramesS;
int iFadeInFramesM;
int iFadeInFramesH;
 
void setup()
{
  size(600,600);
  colorMode(HSB, 3, 1, 1, iFPS);
  frameRate(iFPS);
  
  // グローバル変数初期化
  iCurrentS = -1;
  iCurrentM = -1;
  iCurrentH = -1;
  iFadeInFramesS = iFPS;
  iFadeInFramesM = iFPS;
  iFadeInFramesH = iFPS;
}
 
void draw()
{
  fill(0,0,0);
  noStroke();
  rect(0,0,width,height);
  
  //int iNewS = 59;
  //int iNewM = 59;
  //int iNewH = 23;
  int iNewS = second();
  int iNewM = minute();
  int iNewH = hour();
  if( iNewS != iCurrentS )
  {
    iFadeInFramesS = 0;
  }
  if( iNewM != iCurrentM )
  {
    iFadeInFramesM = 0;
  }
  if( iNewH != iCurrentH )
  {
    iFadeInFramesH = 0;
  }
  
  iCurrentS = iNewS;
  iCurrentM = iNewM;
  iCurrentH = iNewH;

  // 現在の矩形までの描画
  //  秒 
  for( int iSecond=1; iSecond<=60; iSecond++ )
  {
    drawRectSecond( iSecond, iFPS, iSecond<=iCurrentS );
  }
  //  分
  for( int iMinute =1; iMinute<=60; iMinute++ )
  {
    drawRectMinute( iMinute, iFPS, iMinute<iCurrentM );
  }
  //  時
  for( int iHour=1; iHour<=24; iHour++ )
  {
    drawRectHour( iHour, iFPS, iHour<iCurrentH );
  }

  // フェード処理
  //  秒
  if( iFadeInFramesS < iFPS )
  {
    if( iCurrentS == 0 )
    {
      // 全部フェードアウト
      for( int iSecond=1; iSecond<=60; iSecond++ )
      {
        drawRectSecond( iSecond, iFPS-iFadeInFramesS, true );
      }  
    }
    else
    {
      // 次の矩形フェードイン
      drawRectSecond( iCurrentS+1, iFadeInFramesS, true );
    }
    iFadeInFramesS++;
  }
  else
  {
    drawRectSecond( iCurrentS, iFPS, true );
  }
  //  分
  if( iFadeInFramesM < iFPS )
  {
    if( iCurrentM == 0 )
    {
      // 全部フェードアウト
      for( int iMinute=1; iMinute<=60; iMinute++ )
      {
        drawRectMinute( iMinute, iFPS-iFadeInFramesM, true );
      }  
    }
    else
    {
      // 次の矩形フェードイン
      drawRectMinute( iCurrentM, iFadeInFramesM, true );
    }
    iFadeInFramesM++;
  }
  else
  {
    drawRectMinute( iCurrentM, iFPS, true );
  }

  //  時
  if( iFadeInFramesH < iFPS )
  {
    if( iCurrentH == 0 )
    {
      // 全部フェードアウト
      for( int iHour=1; iHour<=24; iHour++ )
      {
        drawRectHour( iHour, iFPS-iFadeInFramesH, true );
      }  
    }
    else
    {
      // 次の矩形フェードイン
      drawRectHour( iCurrentH, iFadeInFramesH, true );
    }
    iFadeInFramesH++;
  }
  else
  {
    drawRectHour( iCurrentH, iFPS, true );
  }
}
void drawRectSecond( int iSecond, float fAlpha, boolean bColored )
{
  if( iSecond==0 )
  {
    return;
  }
  
  pushMatrix();
  
  translate( width/1.4, height/1.6 );
  
  int iDegree = 360 * (iSecond-1) / 60;
  rotate( radians(iDegree) );

  if( bColored )
  {
    fill( 2, 1, 1, fAlpha );
  }
  else
  {
    fill( 2, 0, 1, fAlpha );
  }
  rect( width/14, 0, width/12.0, height/160 );
  
  popMatrix();
}
void drawRectMinute( int iMinute, float fAlpha, boolean bColored )
{
  if( iMinute==0 )
  {
    return;
  }
  
  int iRow = (iMinute-1) / 10;
  int iColumn = (iMinute-1) % 10;
  if( bColored )
  {
    fill( 1, 1, 1, fAlpha );
  }
  else
  {
    fill( 1, 0, 1, fAlpha );
  }
  rect( width/14 + iColumn*width/26, height/2.5 + iRow*height/13, width/27, height/14 );
}
void drawRectHour( int iHour, float fAlpha, boolean bColored )
{
  if( iHour==0 )
  {
    return;
  }
  
  if( bColored )
  {
    fill( 0, 1, 1, fAlpha );
  }
  else
  {
    fill( 0, 0, 1, fAlpha );
  }
  rect( width/14 + (iHour-1)*width/28, height/6, width/32, height/8 );
}
```

![kadai03_fukushima_04](images/kadai3/kadai03_fukushima_04.gif)

# 課題2 繰り返し文によるパターン作成

繰り返し（for文）を効果的に使用した美しいパターン画像をデザインしなさい．

## 目的

- 繰り返し文の記述方法に慣れること． 
- 効率的に美しい画像を生成する方法を体験すること．

## 条件

- 画面サイズは**500×500**ピクセルとする．

- 必ず**for文**を使用すること．

- 図形描画命令（point, line, rect, ellipse等）は全てfor文のブロック文 `{ }` の中に記述してあること．
  下図のように．

  ```java
  for(int iX=0; iX<500; iX++)
  {
  	point( iX, 50 );
  	rect( iX, 0, 80, 80 );
  }
  ```
  
- 画像は複雑であるほど評価が高いが，プログラムはできるだけ効率的に設計するよう心がけること．

- プログラム中にコメントを適切に入れること．
  - 日本語表示が上手くいかない場合は簡単な英単語やローマ字などでもよい．
  - 日本語コメントを入れたい場合，Processing バージョン**3.54以前**のものをダウンロードして使うとよい．
    - インストール不要．
    - 最新のバージョン4.0は現在日本語表示にバグがあり．
- 第４回目の授業「制御文」までで学習していない文法や命令を用いることは極力控える．
  
  - **if文**は学習済みだが，使用不可．
  - 静止画作品であるので，setup( )や，draw( )を使ったアニメーションの使用は不可．
  
- 提出用パワーポイントが自分のPC環境で編集できない場合，できるだけスタジオのWindowsPCを使って編集し，提出すること．
  - スタジオのWindowsPCなら確実に編集できるはず．
  - どうしてもスタジオのPCで編集が行えない場合のみ，事前に講師にメールで通知の上，通常のテキストボックスにコードをペーストして提出すること．

## 提出までの流れ

提出期限：**5/23(月) 23:59**

1. Teams課題ページより以下のリソースをダウンロードする．

   - k2_A00_fukushima.pptx

2. k2_A00_fukushima.pptx のファイル名を以下のように変更する．

   - **k2\_クラス+クラス番号\_苗字** 
   - 例：k2_A02_akama,   k2_B16_shibata,  k2_C22_saitou

3. Processingを使用して作品を制作する．

   - 早めにスケッチブック（フォルダ）とスケッチ(.pde)の名前をステップ2.のpptxファイルと同じ名前にしておく．

4. 画像が完成したら，Processing の実行ウィンドウ（画像が表示されているウィンドウ）のスクリーンショットを撮影． Windows標準のペイントソフトなどにペーストし，そのままpngファイルとして保存する．

   - 最前面のウィンドウのスクリーンショットの撮り方

     - Windows

       「[Alt] + [PrtSc(PrintScreen)]」

     - Mac
   
       「[shift]キー＋[command]キー＋[4]キー→[Space]キー」

5. スケッチブック(pdeが入ったフォルダ)をzip形式に圧縮する.

   - 圧縮の前に，スケッチブック（フォルダ）とスケッチ(.pde)の名前が「k2\_クラス+クラス番号\_苗字」になっているか確認．
   - **k2\_クラス+クラス番号\_苗字.zip** が出来上がるはず．

6. pptxファイルをパワーポイントで開き
   - 右に表示されているサンプル画像を自分の作品のスクリーンショット(png)に差し替える．
   - 左のテキストボックス内のプログラムを自分の作品のプログラムに差し替える．
   - 保存する．

![ppt](images/kadai2/ppt.png)

7. ファイルの提出
   - Teams課題2のページで「+作業の追加」をクリックして，出来上がった以下の２つのファイルをアップロードしてください．
     - **k2\_クラス+クラス番号\_苗字.pptx**
     - **k2\_クラス+クラス番号\_苗字.zip**

## 評価

- 提出を正しく行えているか．
  - 提出期限を順守する．
  - 提出したファイルがそろっている．
  - ファイル名が指定した形式通り正しく付けられている．
- プログラムの読みやすさ．
  - 適切にコメントが付けられているか．
  - for文やif文のカッコ類が適切な位置に記述されているか．
  - インデント等が正しく行われているか．
- 作成された画像のクオリティ．
  - 審美性
  - プログラムならではの表現か．

## 注意事項

- 課題(HTML)ページのサンプルプログラムを加工した作品（色や形を変えた程度）については評価を行わない．また，ネット上のサンプルや参考書籍のプログラムを応用する場合は，出典を何らかの形式で明記し，どのように応用しているのか説明すること．盗作が疑われる場合は厳しく対処する．
- 本科目で教えていない技術を使用しないこと．独自の関数，配列，クラス，外部ライブラリ等の使用は不可とする．
  for文のみでできることを追求するのがこの課題の目的であるので，そこをはき違えて身勝手な作品をつくらないようにすること．

## 質問・質問について

- 質問する際，「どんな作品をつくりたいのか」「何がわからないのか」「自分でどこまで調べたか」を明確化しておくこと．
- プログラムに関する具体的な質問は，pdeファイルを添付するか，コードのテキストをコピー＆ペーストして文面に記載すること．

## 解説

身近にある「繰り返し」を観察してみましょう

<img src="images/loop/rep_cloud.png" alt="rep_cloud" style="zoom:100%;" />

<img src="images/loop/rep_flower.jpg" alt="rep_flower" style="zoom:100%;" />

<img src="images/loop/rep_lenga.png" alt="rep_lenga" style="zoom:100%;" />

<img src="images/loop/rep_tile.png" alt="rep_tile" style="zoom:100%;" />

<img src="images/loop/rep_tree.png" alt="rep_tree" style="zoom:100%;" />

<img src="images/loop/rep_kaidan.jpg" alt="rep_kaidan" style="zoom:100%;" />

<img src="images/loop/rep_mansion.jpg" alt="rep_mansion" style="zoom:100%;" />



## 参考作品

### sample01

```
size(500,500);
background(50);
colorMode(RGB,100);
stroke(200);
 
float fx=0;
float fy=20;
 
for(int ix=0;ix<500;ix+=40)
{
  for(int iy=0;iy<500;iy+=40)
  {
  
  noFill();
 
  stroke(random(0,100),random(0,100),random(0,100));//枠線色
  triangle(fx+ix,fy+iy,fx+10+ix,fy-20+iy,fx+ix+20,fy+iy);//上向きの三角形
  triangle(fx+ix+5 ,fy+iy+10,fx+10+ix,fy+iy,fx+ix+15,fy+iy+10);//内側の三角形
  triangle(fx+iy,fy+ix,fx+10+iy,fy+20+ix,fx+iy+20,fy+ix);//下向きの三角形
  triangle(fx+iy+5,fy+ix-10,fx+10+iy,fy+ix,fx+iy+15,fy+ix-10);//内側の三角形
 
  }
}
```

![s02](images/kadai2/s01.png)

------

### sample02

```
size(500,500);
background(0);
 
float x=0;
float y=0;
 
for(int iX=0; iX<100 && iX<100; iX++)
{
  x=x+6;
  y=y+5;
  stroke(10, x+100, y+200,150);
  noFill();
  ellipse(x,y,x-400,y+50);
}
  
x=0;
y=100;
for(int iY=0; iY<100 && iY<100; iY++)
{
  x=x+4;
  y=y+6;
  stroke(10, x+100, y+200,150);
  noFill();
  ellipse(x,y,x+180,y-200);
}
```

![s01](images/kadai2/s02.png)

------

### sample03

```
size(500,500);
colorMode(RGB,256,256,256,0.1);
background(66,20,33);
int a = 25; //正方形の大きさを指定
float b = a/3*2;//3分の2の辺
float c  =a/3; //3分の1の辺
float r = a*sin(120); //三角形の高さ
translate (-a,a+r*0.3);//入れ子のズレ
 
for(int pt=0; pt<3; pt++)  //2つの図柄を３回繰り返す
{
    //図柄1
    for(int y=0; y<2; y++)       //縦に2繰り返し
    {   
       for(int x=0; x<10; x++)      //横に10繰り返し
       {
          translate(a*2,0);
     
            for(int i=0; i<4; i++)   //1ブロックを4回繰り返し
            {   
              noStroke();
              rotate(PI/2);//角度を90度変える
              //色、大きさの違う三段の三角形を作成
              //配色1
              fill(235,212,134); //クリーム
              triangle(0, 0, 0, a, a, a);
              fill(66,20,33); //焦茶
              triangle(0, 0, 0, b, b, b);
              fill(235,212,134);//クリーム
              triangle(0, 0, 0, c, c, c);
              //配色2
              fill(132,54,26); //茶
              triangle(0, 0, a, 0, a, a);
              fill(217,152,67); //ベージュ
              triangle(0, 0, b, 0, b, b);
              fill(132,54,26);//茶
              triangle(0, 0, c, 0, c, c);
            }
        }
        translate(-500,0); //X座標を左端に戻す
        translate(0,50); //Y座標を一段下げる
    }
  
    translate(a,r*0.6);//図柄と図柄の感覚を開ける
  
    //図柄2
    for(int y=0; y<2; y++)//縦に2個
    {
        for(int x=0; x<21; x++)//横に10個複製
        {
            for(int tri = 0; tri<3; tri++)//正三角形を三分割する
            {
              stroke(66,20,33);
              fill(217,random(150,165),67);
              triangle(0, 0, -a, r, a, r);
              rotate(radians(120));
            }
          scale(1,-1);  //複製するごとに上下を反転する
          translate(a,r); //横に移動、反転時の上下のズレ
        }
      translate(-525,0);
      translate(0,-50+(r/2));
    }
    translate(-a,100-r*0.9);
}
```

![s03](images/kadai2/s03.png)

------

### sample04

```
size(500, 500);
colorMode(RGB, 500, 500, 500);//カラーモード（RGB、500段階）
background(500, 500, 500);//背景白
noFill();//塗りつぶしなし
strokeWeight(0.5);//線の太さを設定
for (int i=0; i<220; i=i+2)
{//110回繰り返し
  stroke(500, 400, 0,100);//線の色をやや透明の黄色に設定
  ellipse(250, 300+i, i+35, i+35);
  //繰り返しごとに大きさ、位置が変わるように円を描く
  ellipse(250, 200-i, i+35, i+35);
  ellipse(300+i, 250, i+35, i+35);
  ellipse(200-i, 250, i+35, i+35);
  ellipse(200+i, 200+i, i+35, i+35);
  //繰り返しごとに大きさ、位置が変わるように円を描く
  ellipse(200+i, 300-i, i+35, i+35);
  ellipse(300-i, 200+i, i+35, i+35);
  ellipse(300-i, 300-i, i+35, i+35);
  stroke(500, 2*i, 500-2*i,100);
  //線の色を外側にいくほどピンクからオレンジに変わるように設定
  ellipse(250, 250+i, i+70, i+70);
  //繰り返しごとに大きさ、位置が変わるように円を描く
  ellipse(250, 250-i, i+70, i+70);
  ellipse(250+i, 250, i+70, i+70);
  ellipse(250-i, 250, i+70, i+70);
  ellipse(250+0.7*i, 250+0.7*i, i+70, i+70);
  //繰り返しごとに大きさ、位置が変わるように円を描く
  ellipse(250+0.7*i, 250-0.7*i, i+70, i+70);
  ellipse(250-0.7*i, 250+0.7*i, i+70, i+70);
  ellipse(250-0.7*i, 250-0.7*i, i+70, i+70);
}
```

![e01](images/kadai2/e01.png)

------

### sample05

```
size(500,500);
colorMode(RGB,500,500,500);
background(500,0,0);
strokeWeight(2.5);
stroke(500,500,500);
for(int x=0; x<600; x=x+50)
{
  for(int y=0; y<600; y=y+100)
  {
    quad(x+0,-20+y,
    x+0,y+30,
    x+10,y+50,
    x+10,y);
    quad(x+15,y+50,
    x+15,y,
    x+25,-20+y,
    x+25,y+30);
    quad(x+25,y+30,
    x+25,y+80,
    x+35,y+100,
    x+35,y+50);
    quad(x+40,y+100,
    x+40,y+50,
    x+50,y+30,
    x+50,y+80);
  }
}
for(int x=0; x<600; x=x+50)
{
  for(int y=0; y<600; y=y+100)
  {
    line(x+12,y+55,x+12,y+100);
    line(x+37,y+0,x+37,y+50);
  }
}
```

![e02](images/kadai2/e02.png)

------

### sample06

```
//青梅波という和柄パターンです。波模様の厚みや大きさなど適宜変更可能
size(500,500);
ellipseMode(CORNER);
strokeWeight(5);
int r = 45; //波模様の半径。strokeWeightと一緒に調整すると吉
float d = 0.55; //円と真下の円との間隔。d=1のとき2r分
int n = 5; //波の層がn層になる
for(int k=0;(k-1)*r*d<=500+r;k++)
{
  //縦幅500を埋めたらそれ以上を描画しないよう調整
  for(int j=0;(j-1)*2*r<=500;j++)
  { //横幅も同様。
    for(int i=n;i>=1;i--)
    {
      stroke(58,143-k,183-k); //軽いグラデーション
      ellipse(r-r*i/n + j*2*r -k%2*r,
      //-k%2*rで一段ずつ噛み合うようにずらす
      -r*i/n + k*r*d,
      i*2*r/n,
      i*2*r/n);
    }
  }
}
```

![e03](images/kadai2/e03.png)

------

### sample07

```
size(500,500);
colorMode(RGB,100);//RGB 100dankai
background(0);//black haikei
stroke(10,55,60);//green?
strokeWeight(2);
noFill();//sikakuno sendakenisuru
int a=15;
int c=4*a;
for(int y=-1; y<500 ;y++)
{
  for(int x=-2; x<500 ;x=x+2)
  {
    quad(c*x+0,c*y+40,c*x+20,c*y+10,c*x+60,c*y+10,c*x+40,c*y+40);//sikaku_a1
    quad(c*x+0,c*y+40,c*x+20,c*y+70,c*x+60,c*y+70,c*x+40,c*y+40);//sikaku_b1
    quad(c*x+60,c*y+10,c*x+100,c*y+10,c*x+120,c*y+-20,c*x+80,c*y+-20);//sikaku_a2
    quad(c*x+60,c*y+70,c*x+80,c*y+100,c*x+120,c*y+100,c*x+100,c*y+70);//sikaku_b2
    line(y*60,0,y*60,500);//tate
    line(c*x+0,c*y+40,c*x+60,c*y+10);//naname_ue
    line(c*x+0,c*y+40,c*x+60,c*y+70);//naname_sita
    line(c*x+60,c*y+10,c*x+120,c*y+-20);//naname_ue2
    line(c*x+60,c*y+70,c*x+120,c*y+100);//naname_sita2
  }
}
```

![e04](images/kadai2/e04.png)

------

### sample08

```
size(500,500);
background(0);
noStroke();
fill(255,255,0);
float c;
for(int a = 25; a <= 500; a += 50)
{
  for(int b = 25; b <= 500; b += 50)
  {
    c = random(0,360);
    arc(a,b,40,40,radians(c),radians(c+300));
  }
}
```

![e05](images/kadai2/e05.png)

------

### sample09

```
size(500,500);
background(255);
noStroke();
for(int i= 0;i <= width; i+=25)
{
  for(int j = 0;j <= height;j+=25)
  {
    fill(255,255-i,255-j,150);
    ellipse(j,i,25 -j/10,25 -j/10);
  }
}
for(int i= 0;i <= width; i+=25)
{
  for(int j = 0;j <= height;j+=25)
  {
    fill(255-i,255-j,255,150);
    ellipse(i,j,25 -j/10,25 -j/10);
  }
}
```

![e06](images/kadai2/e06.png)

------

### sample10

```
size(500,500);
colorMode(HSB, 100); //colormodeHSB 100
background(0,0,0); //background-black
for(int i=0; i<5; i++)
{ //5kai kurikaesu
  noStroke(); //sennashi
  fill(random(0,8),100,100,30); //red~yellow,alpha30
  rect(i*100,0,25,500); //tateline 1
  rect(i*100+30,0,25,500); //tateline 2
  rect(0,i*100,500,25); //yokoline 1
  rect(0,i*100+30,500,25); //yokoline 2
  for(int k=0; k<100; k++)
  { //100 kai kurikaesu
    stroke(10,100,100); //line color yellow
    strokeWeight(2);
    point(k*5,i*100+77); //tensentate
    point(i*100+77,k*5); //tensenyoko
  }
}
```

![e07](images/kadai2/e07.png)

------

### sample11

```
size(500,500);
background(30);
stroke(240,100);
strokeWeight(0.1);
noFill();
float r = 100;
float centx= width/2;
float centy= height/2;
float x, y;
float a, b, c, d, e, f;
float a2, b2, c2, d2, e2, f2;
float a3, b3, c3, d3, e3, f3;
for(float ang2 = 0; ang2 < 360; ang2 = ang2 + 360 / 12)
{
  for(float ang= 0; ang< 360; ang= ang+ 3)
  {
    float rad = radians(ang); //角度をラジアンに変換する
    float rad2 = radians(ang2);
    x = centx+ (r * cos(rad))*1.3;
    y = centy+ (r * sin(rad))*1.3;
    /*
    ↓の図形を円形に回転させて花びら的なものを作る
    bezier(centx, height*0.2, x-cos(rad)*70, y-sin(rad)*70-30, x-cos(rad)*50, y-sin(rad)*50-50, centx, centy);
    bezier(centx, 0, x-cos(rad)*50, y-sin(rad)*50-60, x-cos(rad)*20, y-sin(rad)*20-80, centx, centy);
    bezier(centx, height*-0.5, x, y-110, x-cos(rad)*-30, y-sin(rad)*-30-130, centx, centy);
    */
    a3 = centx+ height * cos(rad2-45);
    b3 = centy+ height * sin(rad2-45);
    c3 = x + 110 * cos(rad2);
    d3 = y + 110 * sin(rad2);
    e3 = x -cos(rad) * -30 + 130 * cos(rad2);
    f3 = y -sin(rad) * -30 + 130 * sin(rad2);
    bezier(a3, b3, c3, d3, e3, f3, centx, centy); //図形(大)
    a2 = centx+ height * 0.5 * cos(rad2 + 11.25);
    b2 = centy+ height * 0.5 * sin(rad2 +11.25);
    c2 = x -cos(rad) * 50 + 60 * cos(rad2);
    d2 = y -sin(rad) * 50 + 60 * sin(rad2);
    e2 = x -cos(rad) * 20 + 80 * cos(rad2);
    f2 = y -sin(rad) * 20 + 80 * sin(rad2);
    bezier(a2, b2, c2, d2, e2, f2, centx, centy);//図形(中)
    a = centx+ height * 0.3 * cos(rad2);
    //先端が中心(centx,centy)からheight*0.3離れて回転
    b = centy+ height * 0.3 * sin(rad2);
    c = x -cos(rad) * 70 + 30 * cos(rad2);
    d = y -sin(rad) * 70 + 30 * sin(rad2);
    e = x -cos(rad) * 50 + 50 * cos(rad2);
    f = y -sin(rad) * 50 + 50 * sin(rad2);
    bezier(a, b, c, d, e, f, centx, centy); //図形(小)
  }
}
```

![e08](images/kadai2/e08.png)

------

### sample12

```
size(500, 500);
background(0); //背景を黒に設定
colorMode(RGB,500,500,500); //カラーモード(RGB, 500段階)
noFill(); //塗りつぶしなし
for(int i=0; i<500; i=i+25)
{ //20回繰り返し
  stroke(500, 500, i); //RGBのBに変数を使用
  ellipse(0, i, 500, 500);
  ellipse(500, i, 500, 500);
  stroke(i, 500, 500); //RGBのRに変数を使用
  ellipse(i, 0, 500, 500);
  ellipse(i, 500, 500, 500);
}
```

![e09](images/kadai2/e09.png)

------

### sample13

```
size(500, 500);
colorMode(HSB, 100);//カラーモード（HSB、100段階）
noStroke();//枠線なし
float a, b; //変数（a=外円の直径、b=内円の直径）
for(int i=0; i<500; i=i+50)
{//10回繰り返し
  for(int j=0; j<500; j=j+50)
  {//10回繰り返し
    fill(random(70, 80), 50, 80);//紫っぽい色に設定
    a=random(30, 50);//30〜50の間の乱数を求める
    ellipse(i+25, j+25, a, a);//外側の円の描画
    fill(random(10, 30), 50, 80);//黄色〜黄緑
    b=random(0, 20);//0〜20の間の乱数を求める
    ellipse(i+25, j+25, b, b);//内側の円の描画
  }
}
```

![e10](images/kadai2/e10.png)

------

### sample14

```
size(500, 500);
float sx=0,sy=0; //変数の宣言（正方形の左上X、Y座標）
float px, py; //変数の宣言（正方形の中心のズレX,Y座標）
for(int i=0; i<10; i++)
{
  sx=i*50; //正方形の開始地点のX座標を計算
  for(int j=0; j<10; j++)
  {
    sy=j*50; //正方形の開始地点のY座標を計算
    px=random(5);//乱数でズレを求める
    py=random(5);
    for(int k=0; k<10; k++)
    {
      rect(sx+px*k, sy+py*k, 50-(k*5), 50-(k*5));
    }
  }
}
```

![e11](images/kadai2/e11.png)

### 2021年度参考作品

課題2提出作品集2021.pptx

- Teams->「ファイル」タブ からダウンロード可能．

  

# 課題1 位置情報の色情報化

配布されているプログラムを書き換えることで，美的な画像を生成する． 位置情報を数値計算によって色情報に変換する処理の編集を行う．

## 目的

- 課題の提出方法に慣れる．
  - 提出フォームの使い方．
  - pptテンプレートファイルの編集．
  - スケッチブック（フォルダ）をzipファイルへ圧縮．
- 数値計算の記述に慣れる．
  - 学習した命令を組み合わせる．
  - トライアンドエラーの繰り返し．

## 提出までの流れ

提出期限：**5/9(月) 23:59**

1. Teams課題ページより以下のリソースをダウンロードする．

   - k1_A00_fukushima.pptx
   - k1_A00_fukushima.zip

2. A00_fukushima.zip を解凍ソフトで解凍する．

3. 解凍後，中の**フォルダ名とpdeファイルのファイル名の両方**を以下のように変更する．

   - **k1\_クラス_+クラス番号\_苗字** 
   - 例：k1_A02_akama,   k1_B16_shibata,  k1_C22_saitou

4. **k1\_A00_fukushima.pptx のファイル名も上記のように変更しておく**．

5. Processingを使用して，pde 内のプログラム/* `Example` */以下3行部分に独自の変更を加え実行し，画像を表示する．

   `/* Example */`直後の三つの文における**右辺**の記述を様々に書き換え，いろいろな画像が出力されることを確認する．

   ```
   size(500, 500);      // Window Size
   colorMode(RGB, 1.0); // RGB(0.0~1.0)
   float fR,fG,fB;      // Window RGB
   for(int iX=0; iX<width; iX++)
   {
     for(int iY=0; iY<height; iY++)
     {
       /* Example */
       fR = sin(iX/10.0)/2.0+0.5;
       fG = sin(iX/10.0)/2.0+0.5;
       fB = sin(iX/10.0)/2.0+0.5;
       // draw pixel
       stroke(fR, fG, fB);
       point(iX,iY);
     }
   }
   ```

![kadai1_sample00](images/kadai1/kadai1_sample00.png)

1. 画像が完成したら，Processing の実行ウィンドウ（画像が表示されているウィンドウ）のスクリーンショットを撮影． Windows標準のペイントソフトなどにペーストし，そのままpngファイルとして保存する．

   - 最前面のウィンドウのスクリーンショットの撮り方

     - Windows

       「[Alt] + [PrtSc(PrintScreen)]」

     - Mac

       「[shift]キー＋[command]キー＋[4]キー→[Space]キー」

2. スケッチブック(pdeが入ったフォルダ)をzip形式に圧縮する.

   - **k1\_クラス+クラス番号\_苗字.zip** が出来上がるはず．

![zip_kouzou](images/kadai/zip_kouzou.png)

1. pptxファイルをパワーポイントで開き
   - 右に表示されているサンプル画像を自分の作品のスクリーンショット(png)に差し替える．
   - 左のテキストボックス内のプログラムを自分の作品のプログラムに差し替える．
   - 保存する．

![kadai1_template](images/kadai1/kadai1_template.png)

1. ファイルの提出
   - Teams課題１のページで「+作業の追加」をクリックして，出来上がった以下の２つのファイルをアップロードしてください．
     - **k1\_クラス+クラス番号\_苗字.pptx**
     - **k1\_クラス+クラス番号\_苗字.zip**

## 条件

- プログラム中の指示した箇所以外に変更を加えないこと．
- 本課題ではコメントは特に必要ない．
- 第３回目の授業「演算と変数」までで学習していない文法や命令を用いることは極力控える．

## 評価

- 提出を正しく行えているか．
  - 提出期限を順守する．
  - 提出したファイルがそろっている．
  - ファイル名が指定した形式通り正しく付けられている．
- プログラムから試行錯誤を行った形跡を感じられるか．
  - 提出画像のクオリティに依らず加点．
- 作成された画像のクオリティ．（加点は少な目）
  - 審美性
  - プログラムならではの表現

## 注意事項

- リファレンスや課題ページのサンプルプログラムを少々加工した作品については評価を行わない．
  - 色や形を変えた程度であったり，明らかに似ていると判断されるものは評価が難しい．
- また，ネット上のサンプルや参考書籍のプログラムを応用する場合は，出典を何らかの形式で明記し，どのように応用しているのか説明すること．盗作が疑われる場合は厳しく対処する．

## 質問・質問について

- 質問する際，「どんな作品をつくりたいのか」「何がわからないのか」「自分でどこまで調べたか」を明確化しておくこと．
- プログラムに関する具体的な質問は，pdeファイルを添付するか，コードのテキストをコピー＆ペーストして文面に記載すること．

## 解説

このプログラムは，ウィンドウ内のすべてのピクセルに`point()`で色付きの点を描画するプログラムである．

1. 以下のような手順の繰り返しによりウィンドウ内の全ピクセルに点描画を行っている．
   1. 下の図のように原点から描画が始まる．
   2. 上から下へ縦方向へ点を描画．
   3. 横へ1ピクセルずれる．
   4. 手順2.へ戻る．

![repeate_graph](images/kadai1/repeate_graph.png)

2. このような繰り返し処理はfor文で行っている．

![repeate_flow_setsumei](images/kadai1/repeate_flow_setsumei.png)

3. ピクセルカラー`fR, fG, fB`に代入する式を考える．
   - ピクセルの座標値`iX`,` iY`を活用する．
   - 様々な式や数値を組み合わせる．（すべてを使用する必要はない）
     - 四則演算
     - 三角関数 `sin()`, `cos()`, `tan()`
     - ランダム値 `random()`




## 参考作品

### Sample01

```
size(500, 500);      // Window Size
colorMode(RGB, 1.0); // RGB(0.0~1.0)
float fR,fG,fB;      // Window RGB
for(int iX=0; iX<width; iX++)
{
  for(int iY=0; iY<height; iY++)
  {
    fR=sin(iX/10.0)*sin(iY/10.0);
    fG=cos(iX/15.0)*cos(iY/15.0);
    fB=sin(iX/20.0)*sin(iY/20.0);
    // draw pixel
    stroke(fR, fG, fB);
    point(iX,iY);
  }
}
```

![kadai1_sample01](images/kadai1/kadai1_sample01.png)

### Sample02

```
size(500, 500);      // Window Size
colorMode(RGB, 1.0); // RGB(0.0~1.0)
float fR,fG,fB;      // Window RGB
for(int iX=0; iX<width; iX++)
{
  for(int iY=0; iY<height; iY++)
  {
    fR=cos(iX*iX/30.0)*cos(iY*iY/30.0);
    fG=sin(iY*iY/60.0)*cos(iX*iX/60.0);
    fB=sin(iX*iX/120.0)*cos(iY*iY/120.0);

    // draw pixel
    stroke(fR, fG, fB);
    point(iX,iY);
  }
}
```

![kadai1_sample02](images/kadai1/kadai1_sample02.png)

### Sample03

```
size(500, 500);      // Window Size
colorMode(RGB, 1.0); // RGB(0.0~1.0)
float fR,fG,fB;      // Window RGB
for(int iX=0; iX<width; iX++)
{
  for(int iY=0; iY<height; iY++)
  {
    fR=tan(iX*iY/200.0);
    fG=(tan(iX*iY/100.0)*(-1.0));
    fB=tan(iX*iY/250.0);
    
    // draw pixel
    stroke(fR, fG, fB);
    point(iX,iY);
  }
}
```

![kadai1_sample03](images/kadai1/kadai1_sample03.png)

### Sample04

```
size(500, 500);      // Window Size
colorMode(RGB, 1.0); // RGB(0.0~1.0)
float fR,fG,fB;      // Window RGB
for(int iX=0; iX<width; iX++)
{
  for(int iY=0; iY<height; iY++)
  {
    /* Example */
    fR=1/((pow(iX-250,2)/iY+pow(iY-100,2)/iY)/50.0);
    fG=0.0;
    fB=(pow(iX-250,2)+pow(iY-250,2))/(500*iY);

    // draw pixel
    stroke(fR, fG, fB);
    point(iX,iY);
  }
}
```

![kadai1_sample03](images/kadai1/kadai1_sample04.png)

### Sample05

```
size(500, 500);      // Window Size
colorMode(RGB, 1.0); // RGB(0.0~1.0)
float fR,fG,fB;      // Window RGB
for(int iX=0; iX<width; iX++)
{
  for(int iY=0; iY<height; iY++)
  {
    fR=tan((pow(iX-250,2)+pow(iY-250,2))/50);
    fG=sin((pow(iX-250,2)+pow(iY-250,2))/150);
    fB=cos((pow(iX-250,2)+pow(iY-250,2))/100);

    // draw pixel
    stroke(fR, fG, fB);
    point(iX,iY);
  }
}
```

![kadai1_sample05](images/kadai1/kadai1_sample05.png)

### 2021年度参考作品

課題1提出作品集2021.pptx

- Teams->「ファイル」タブ からダウンロード可能．

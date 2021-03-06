---
title: テキストとグラフィックスの統合
description: この記事では、Xamarin.Forms アプリケーションに SkiaSharp グラフィックスとテキストを統合するレンダリングされたテキスト文字列のサイズを決定する方法について説明し、サンプル コードを示します。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B5AC82-7736-4AD8-AA16-FE43E18D203C
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: be1524029ada79896f83517c3b439f2ad0e2c6d9
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615162"
---
# <a name="integrating-text-and-graphics"></a>テキストとグラフィックスの統合

_SkiaSharp のグラフィックスとテキストを統合するレンダリングされたテキスト文字列のサイズを決定する方法を参照してください。_

この記事では、テキストを計測、可能性がある特定のサイズにテキストを拡大およびその他のグラフィックスとテキストを統合する方法を示しています。

![](text-images/textandgraphicsexample.png "四角形で囲まれたテキスト")

SkiaSharp、`Canvas`クラスには、四角形を描画するメソッドも含まれています ([`DrawRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawRect/p/SkiaSharp.SKRect/SkiaSharp.SKPaint/)) と角の丸い四角形 ([`DrawRoundRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawRoundRect/p/SkiaSharp.SKRect/System.Single/System.Single/SkiaSharp.SKPaint/))。 これらのメソッドとして定義される四角形を必要とする`SKRect`値。

**テキストを囲む**ページ センター ページとの角の丸い四角形のペアから成るフレームでグループ化に短いテキスト文字列。 [ `FramedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs)クラスを行う方法を示しています。

SkiaSharp を使用して、`SKPaint`セットのテキストとフォント属性をクラスも使用できます、レンダリングされるテキストのサイズを取得します。 次の先頭`PaintSurface`イベント ハンドラーを呼び出す 2 つの異なる`MeasureText`メソッド。 最初の[ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/)の呼び出しでは、単純な`string`引数と現在のフォント属性に基づいて、テキストのピクセル幅を返します。 プログラムは、新しい計算`TextSize`のプロパティ、`SKPaint`オブジェクトが現在その描画時の幅に基づく`TextSize`プロパティ、および表示領域の幅。 設定するためのものがこの`TextSize`文字列が画面の幅の 90% に表示されるようにします。

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string str = "Hello SkiaSharp!";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Chocolate
    };

    // Adjust TextSize property so text is 90% of screen width
    float textWidth = textPaint.MeasureText(str);
    textPaint.TextSize = 0.9f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(str, ref textBounds);
    ...
}
```

2 番目の[ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/SkiaSharp.SKRect@/)の呼び出しでは、`SKRect`引数、幅と表示されるテキストの高さの両方を取得するようにします。 `Height`プロパティのこの`SKRect`値は、英大文字、アセンダー、およびテキスト文字列内のディセンダーの存在に依存します。 異なる`Height`値がテキスト文字列"mom"、"cat"および"dog"、たとえば報告されます。

`Left`と`Top`のプロパティ、`SKRect`によってテキストが表示されている場合、構造体がレンダリングされたテキストの左上隅の座標を示します、`DrawText`呼び出しが 0 の位置 X と Y 位置。 IPhone 7 シミュレーターでは、このプログラムが実行されている場合など、 `TextSize` 90.6254 最初の呼び出しを次の計算の結果としての値が割り当てられている`MeasureText`します。 `SKRect` 2 番目の呼び出しから取得した値`MeasureText`は次のプロパティ値があります。

- `Left` = 6
- `Top` = &ndash;68
- `Width` = 664.8214
- `Height` = 88;

留意すること、X 座標と Y 座標を渡す、`DrawText`メソッドは、基本的には、テキストの左側にあるを指定します。 `Top`値では、テキストが 68 ピクセル ベースラインで (88 から 68 it を減算) 上に拡張されていることを示します、ベースラインを下回る 20 ピクセルです。 `Left`値 6 は、6 ピクセルの X 値の右側に始まることを示します、`DrawText`呼び出します。 これにより、通常の文字間の間隔。 ディスプレイ画面の左上隅にぴったり収まりましたテキストを表示する場合は、これらの欠点を渡す`Left`と`Top`座標 X と Y 座標と値の`DrawText`、この例で&ndash;6 および 68。

`SKRect`構造は、いくつかの便利なプロパティとメソッド、うちいくつかの残りの部分で使用を定義、`PaintSurface`ハンドラー。 `MidX`と`MidY`値は、四角形の中心の座標を示します。 (IPhone 7 の例でこれらの値が 338.4107 と&ndash;24)。次のコードでは、座標の最も簡単な計算のこれらの値を使用して、中央揃えで表示。

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(str, xText, yText, textPaint);
    ...
}
```

`PaintSurface`ハンドラーの 2 つの呼び出しで終了`DrawRoundRect`の引数を必要とする`SKRect`します。 これは、`SKRect`値が確実のような`SKRect`から取得した値、`MeasureText`メソッドは同じであることはできません。 最初に、そのように角丸四角形は、テキストの端を描画しないと、領域にシフトするときに必要なもう 1 つは、少し大きい方がする必要があるように、`Left`と`Top`値はするが、四角形の左上隅に対応配置されています。 これら 2 つのジョブは、によって実現されます`Offset`と`Inflate`によって定義されたメソッド`SKRect`:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Create a new SKRect object for the frame around the text
    SKRect frameRect = textBounds;
    frameRect.Offset(xText, yText);
    frameRect.Inflate(10, 10);

    // Create an SKPaint object to display the frame
    SKPaint framePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5,
        Color = SKColors.Blue
    };

    // Draw one frame
    canvas.DrawRoundRect(frameRect, 20, 20, framePaint);

    // Inflate the frameRect and draw another
    frameRect.Inflate(10, 10);
    framePaint.Color = SKColors.DarkBlue;
    canvas.DrawRoundRect(frameRect, 30, 30, framePaint);
}
```

次に、メソッドの残りの部分は簡単です。 別に作成します`SKPaint`オブジェクトの枠線と呼び出し`DrawRoundRect`2 回です。 2 番目の呼び出しでは、拡大しても、別の 10 ピクセルの四角形を使用します。 最初の呼び出しを 20 ピクセルの角の半径を指定します2 つ目は並列がないように見えるように 30 (ピクセル単位) の角の半径があります。

 [![](text-images/framedtext-small.png "フレームのテキスト ページのスクリーン ショットをトリプル")](text-images/framedtext-large.png#lightbox "囲むテキスト ページの 3 倍になるスクリーン ショット")

フレーム サイズが大きくなるとテキストを表示するには、電話またはシミュレーターを横方向にすることができます。

のみ画面にいくつかのテキストを中央揃えにする必要がある場合は設定で、テキストを測定することがなく約を行うことができます、`TextAlign`プロパティの`SKPaint`に`SKTextAlign.Center`します。 指定した X 座標、`DrawText`しメソッドでは、テキストの水平方向の中心が配置されていることを示します。 画面の中間点を渡した場合、`DrawText`メソッドでは、テキストは水平方向に中央揃えと*ほぼ*ベースラインに垂直方向に表示するために垂直方向に中央揃え。

テキスト自体は、グラフィカル オプションと同様、はるか扱うことができます。 単純な 1 つのオプションは、通常の塗りつぶされた表示ではなく、テキスト文字のアウトラインを表示するのには。

[![](text-images/outlinedtext-small.png "中抜きの文字列をページのスクリーン ショットのトリプル")](text-images/outlinedtext-large.png#lightbox "3 倍に記載されているテキスト ページのスクリーン ショット")

法線を変更するだけでこれは、`Style`のプロパティ、`SKPaint`オブジェクトの既定の設定から`SKPaintStyle.Fill`に`SKPaintStyle.Stroke`とストロークの幅を指定することで。 `PaintSurface`のハンドラー、**中抜きの文字列を**ページは、これをどのように表示されます。

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "OUTLINE";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 1,
        FakeBoldText = true,
        Color = SKColors.Blue
    };

    // Adjust TextSize property so text is 95% of screen width
    float textWidth = textPaint.MeasureText(text);
    textPaint.TextSize = 0.95f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(text, ref textBounds);

    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(text, xText, yText, textPaint);
}
```

 過去のいくつかの記事でが SkiaSharp を使用してテキストを描画する方法を説明するようになりました円、楕円、および角の丸い四角形。 次の手順は[SkiaSharp の線とパス](~/xamarin-forms/user-interface/graphics/skiasharp/paths/paths.md)でグラフィック パスに接続されている直線を描画する方法が説明されます。


## <a name="related-links"></a>関連リンク

- [SkiaSharp の Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (サンプル)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

---
title: Xamarin.Forms との統合
description: この記事では、Xamarin.Forms 要素、およびタッチに応答する SkiaSharp グラフィックを作成する方法について説明し、サンプル コードを示します。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 288224F1-7AEE-4148-A88D-A70C03F83D7A
author: charlespetzold
ms.author: chape
ms.date: 02/09/2017
ms.openlocfilehash: 23dcc6f11f40283a220aba47b33717e7e5740dbe
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615848"
---
# <a name="integrating-with-xamarinforms"></a>Xamarin.Forms との統合

_タッチと Xamarin.Forms 要素に応答する SkiaSharp グラフィックスを作成します。_

SkiaSharp のグラフィックスは、Xamarin.Forms のいくつかの方法で残りの部分と統合できます。 SkiaSharp のキャンバスと同じ ページで、Xamarin.Forms 要素 SkiaSharp キャンバス上にあっても位置 Xamarin.Forms 要素を組み合わせることができます。

![](integration-images/integrationexample.png "スライダーの色の選択")

Xamarin.Forms で SkiaSharp の対話型のグラフィックスを作成するもう 1 つの方法は、タッチ機能によるです。
2 番目のページで、 [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)プログラムが使用権を持って**トグルの塗りつぶしをタップ**します。 2 つの方法の単純な円を描画&mdash;塗りつぶしと塗りつぶしを&mdash;をタップして切り替えます。 [ `TapToggleFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml.cs)クラスは、ユーザー入力への応答で SkiaSharp グラフィックを変更する方法を示しています。

このページで、`SKCanvasView`でクラスをインスタンス化、 [TapToggleFill.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml)ファイルで、また、Xamarin.Forms を設定[ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer)ビュー。

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.TapToggleFillPage"
             Title="Tap Toggle Fill">

    <skia:SKCanvasView PaintSurface="OnCanvasViewPaintSurface">
        <skia:SKCanvasView.GestureRecognizers>
            <TapGestureRecognizer Tapped="OnCanvasViewTapped" />
        </skia:SKCanvasView.GestureRecognizers>
    </skia:SKCanvasView>
</ContentPage>
```

通知、 `skia` XML 名前空間宣言。

`Tapped`のハンドラー、`TapGestureRecognizer`オブジェクトのブール型フィールドと呼び出しの値を切り替えます、 [ `InvalidateSurface` ](https://developer.xamarin.com/api/member/SkiaSharp.Views.Forms.SKCanvasView.InvalidateSurface()/)メソッドの`SKCanvasView`:

```csharp
bool showFill = true;
...
void OnCanvasViewTapped(object sender, EventArgs args)
{
    showFill ^= true;
    (sender as SKCanvasView).InvalidateSurface();
}
```

呼び出し`InvalidateSurface`への呼び出しを効果的に生成されます、`PaintSurface`ハンドラーで、使用、`showFill`フィールドを入力するか、または円は入力しません。

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 50
    };
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);

    if (showFill)
    {
        paint.Style = SKPaintStyle.Fill;
        paint.Color = SKColors.Blue;
        canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    }
}
```

`StrokeWidth`プロパティが、違いを強調したり 50 に設定されています。 最初の内部を描画し、アウトライン、全体の線の幅を確認できます。 既定では、グラフィックスの図の描画、`PaintSurface`イベント ハンドラーでは、ハンドラーの前で描画されるものが見えにくきます。

**色探索**ページと他の Xamarin.Forms 要素 SkiaSharp グラフィックスも統合する方法について説明および SkiaSharp で色を定義するための 2 つの代替方法の違いについても示します。 静的な[ `SKColor.FromHsl` ](https://developer.xamarin.com/api/member/SkiaSharp.SKColor.FromHsl/p/System.Single/System.Single/System.Single/System.Byte/)メソッドを作成、`SKColor`色合い-鮮やかさ-明るさモデルに基づいて、値。

```csharp
public static SKColor FromHsl (Single h, Single s, Single l, Byte a)
```

静的な[ `SKColor.FromHsv` ](https://developer.xamarin.com/api/member/SkiaSharp.SKColor.FromHsv/p/System.Single/System.Single/System.Single/System.Byte/)メソッドを作成、`SKColor`値色合い-鮮やかさ-値の類似のモデルに基づいています。

```csharp
public static SKColor FromHsv (Single h, Single s, Single v, Byte a)
```

どちらの場合で、`h`引数の範囲は 0 ~ 360 です。 `s`、 `l`、および`v`引数の範囲は 0 ~ 100 です。 `a` (アルファまたは不透明度) 引数範囲は 0 ~ 255 です。

[ **ColorExplorePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml)ファイルには、2 つ作成されます`SKCanvasView`内のオブジェクトを`StackLayout`と並行して`Slider`と`Label`HSL を選択するユーザーを許可するビューとHSV 色の値:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Basics.ColorExplorePage"
             Title="Color Explore">
    <StackLayout>
        <!-- Hue slider -->
        <Slider x:Name="hueSlider"
                Maximum="360"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference hueSlider},
                              Path=Value,
                              StringFormat='Hue = {0:F0}'}" />

        <!-- Saturation slider -->
        <Slider x:Name="saturationSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference saturationSlider},
                              Path=Value,
                              StringFormat='Saturation = {0:F0}'}" />

        <!-- Lightness slider -->
        <Slider x:Name="lightnessSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference lightnessSlider},
                              Path=Value,
                              StringFormat='Lightness = {0:F0}'}" />

        <!-- HSL canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hslCanvasView"
                               PaintSurface="OnHslCanvasViewPaintSurface" />

            <Label x:Name="hslLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>

        <!-- Value slider -->
        <Slider x:Name="valueSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference valueSlider},
                              Path=Value,
                              StringFormat='Value = {0:F0}'}" />

        <!-- HSV canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hsvCanvasView"
                               PaintSurface="OnHsvCanvasViewPaintSurface" />

            <Label x:Name="hsvLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>
    </StackLayout>
</ContentPage>
```

2 つ`SKCanvasView`要素は、1 つのセル`Grid`で、`Label`結果の RGB 色の値を表示するための上部にします。

[ **ColorExplorePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml.cs)分離コード ファイルは比較的単純です。 共有`ValueChanged`、3 つのハンドラー`Slider`要素を単に無効化両方`SKCanvasView`要素。 `PaintSurface`ハンドラーによって示される色でキャンバスをクリア、`Slider`要素も設定して、`Label`の上に座って、`SKCanvasView`要素。

```csharp
public partial class ColorExplorePage : ContentPage
{
    public ColorExplorePage()
    {
        InitializeComponent();

        hueSlider.Value = 0;
        saturationSlider.Value = 100;
        lightnessSlider.Value = 50;
        valueSlider.Value = 100;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        hslCanvasView.InvalidateSurface();
        hsvCanvasView.InvalidateSurface();
    }

    void OnHslCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsl((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)lightnessSlider.Value);
        args.Surface.Canvas.Clear(color);

        hslLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }

    void OnHsvCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsv((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)valueSlider.Value);
        args.Surface.Canvas.Clear(color);

        hsvLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }
}
```

HSV と HSL の色モデルでは、色合いの値は、0 ~ 360 範囲し、色の色合いを基準となることを示します。 これらは、虹の従来の色: 赤、オレンジ色、黄、緑、青、indigo、バイオレット、赤に円の中のバックします。

HSL モデルで明るさの値 0 は常に黒、および 100 の値が空白では常にします。 鮮やかさの値が 0 の場合、0 から 100 までの輝度値は、灰色の網掛けになります。 彩度を増やすには、その他の色が追加されます。 (これは、RGB 値 255、0 と 255 までの 0 から 3 番目までの間に等しい 1 つのコンポーネントを)、純粋な色は、鮮やかさは 100 と輝度が 50 に発生します。

HSV モデルでは、純粋な色は、飽和状態と値の両方が 100 を発生します。 値が、他の設定に関係なく、0 の場合は、色は黒です。 鮮やかさが 0 と値の範囲は 0 ~ 100 の場合は、灰色の網かけにあります。

それらを自分で実験の 2 つのモデルを理解する最善の方法です。

[![](integration-images/colorexplore-large.png "色の詳細ページのスクリーン ショットをトリプル")](integration-images/colorexplore-small.png#lightbox "色の詳細ページの 3 倍になるスクリーン ショット")


## <a name="related-links"></a>関連リンク

- [SkiaSharp の Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (サンプル)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

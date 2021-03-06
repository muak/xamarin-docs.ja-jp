---
title: Xamarin.Android で Google Maps と場所を使用する方法
description: この記事では、Xamarin.Android でマップや場所を使用する方法について説明します。 組み込みのマップ アプリケーション V2 を使用して、Google マップ Android API 直接を活用することからすべての情報を説明します。
ms.prod: xamarin
ms.assetid: 425E0ED2-5380-6EBE-7059-256B6E9128B8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/25/2018
ms.openlocfilehash: a861e43152870933ba684bf693a1bd3d3ac5bd0b
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935374"
---
# <a name="how-to-use-google-maps-and-location-with-xamarinandroid"></a>Xamarin.Android で Google Maps と場所を使用する方法

_この記事では、Xamarin.Android でマップや場所を使用する方法について説明します。組み込みのマップ アプリケーション V2 を使用して、Google マップ Android API 直接を活用することからすべての情報を説明します。_

## <a name="maps-overview"></a>マップの概要

マッピング テクノロジは、モバイル デバイスにユビキタスの補数です。 デスクトップ コンピューターとラップトップしない傾向があります組み込みの場所を認識しています。 その一方で、モバイル デバイスは、デバイスを検索して変化する場所の情報を表示する、このようなアプリケーションを使用します。 Android は、デバイスで使用可能な場所のハードウェアを使用して、マップ上の場所データを表示する組み込みの強力なテクノロジがあります。 この記事は、含む提供されている必要がある Xamarin.Android マップのアプリケーションのスペクトルをについて説明します。 

-  組み込みを使用してすばやくマッピング機能を追加するアプリケーションにマップします。
-  マップの表示を制御するマップの API を使用します。
-  さまざまな手法を使用して、グラフィカルなオーバーレイを追加します。

このセクションのトピックでは、広範なマッピングの機能をカバーします。
最初に、これらの場所のパノラマ写真の番地ビューを表示する方法と、Android の組み込みのマップのアプリケーションを活用する方法を説明します。 エラーは、コントロールの位置と、マップの表示に両方の方法を説明する、アプリケーション内で直接マッピング機能を組み込むマップの API を使用すると、グラフィカルなオーバーレイを追加する方法について説明します。


## <a name="related-links"></a>関連リンク

- [MapsAndLocationDemo_v3 (sample)](https://developer.xamarin.com/samples/monodroid/MapsAndLocationDemo_v3/)
- [アクティビティのライフサイクル](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Google マップ API キーを取得する](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [インテント一覧: Android デバイスで Google のアプリケーションの起動](http://developer.android.com/guide/appendix/g-app-intents.html)
- [場所とマップ](http://developer.android.com/guide/topics/location/index.html)

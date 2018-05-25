StaticBluredScreen
===

NOTE: This project has been merged to [UIEffect](https://github.com/mob-sakai/UIEffect).

[![](https://img.shields.io/github/release/mob-sakai/StaticBluredScreen.svg?label=latest%20version)](https://github.com/mob-sakai/StaticBluredScreen/releases)
[![](https://img.shields.io/github/release-date/mob-sakai/StaticBluredScreen.svg)](https://github.com/mob-sakai/StaticBluredScreen/releases)  
![](https://img.shields.io/badge/requirement-Unity%205.5%2B-green.svg)
[![](https://img.shields.io/github/license/mob-sakai/StaticBluredScreen.svg)](https://github.com/mob-sakai/StaticBluredScreen/blob/master/LICENSE.txt)  
[![](https://img.shields.io/github/last-commit/mob-sakai/StaticBluredScreen/develop.svg?label=last%20commit%20to%20develop)](https://github.com/mob-sakai/StaticBluredScreen/commits/develop)
[![](https://img.shields.io/github/issues/mob-sakai/StaticBluredScreen.svg)](https://github.com/mob-sakai/StaticBluredScreen/issues)


<< [Description](#Description) | [WebGL Demo](#demo) | [Download](https://github.com/mob-sakai/StaticBluredScreen/releases) | [Usage](#usage) | [Change log](https://github.com/mob-sakai/StaticBluredScreen/blob/develop/CHANGELOG.md) >>

<br>
<br>
<br>
<br>
<br>
<br>

## Overview

![image](https://user-images.githubusercontent.com/12690315/33933482-3238bd34-e039-11e7-864c-bb22f8be9e5e.png)

静的なブラースクリーンを表示します.
ポストエフェクト等によるリアルタイムブラーとは異なり、ある時点でのレンダリング結果に対するブラーのみを提供します.

1. ブラー処理用のCameraが不要です.
2. ブラーは常時実行されません. テクスチャ更新を実行したあと1度だけ実行されます.
3. 縮小バッファを利用することで、メモリサイズを小さく抑えます.
4. スクリーン | ブラー | ダイアログ1 | ブラー | ダイアログ2 ... のように、重ねて表示できます.
5. 激しい動きのあるオブジェクトがスクリーン上にある場合、ブラーテクスチャにズレが発生し得ます.

![](https://user-images.githubusercontent.com/12690315/33972462-5c8d1d04-e0c1-11e7-8e04-a8089a208f0d.gif)




## How To Work?

カメラに対し以下のコマンドバッファを１フレームだけ割り当てています.  
縮小バッファを利用してブラーを適用し、出力先RTにコピーしているだけです.  
その後、出力先RTをRawImage等で表示させると、ブラーが画面に適用されているように見えます.  
ブラーが適用されるのはあくまで縮小バッファであり、レンダリング負荷がほとんどかからないことに注意してください.

```cs
// テンポラリRTに対するIDを生成.
int copyId = Shader.PropertyToID("_ScreenCopy");
int blurId = Shader.PropertyToID("_StaticBlur");

// ブラーシェーダを割り当てたマテリアルを生成.
Material materialBlur = new Material(shaderBlur);

// 出力先RTを生成.
RenderTexture rt = new RenderTexture(w, h);

// カメラのレンダリング結果をRTにコピー.
buffer = new CommandBuffer();
buffer.GetTemporaryRT(copyId, -1, -1, 0, FilterMode.Bilinear);
buffer.Blit(BuiltinRenderTextureType.CurrentActive, copyId);

// 縮小バッファを利用して、ブラーシェーダを適用.
buffer.GetTemporaryRT(blurId, w/4, h/4, 0, FilterMode.Bilinear);
buffer.Blit(copyId, blurId, materialBlur);

// 縮小バッファから出力先RTにコピー.
buffer.Blit(blurId, rtId);

// テンポラリRTを解放.
buffer.ReleaseTemporaryRT(blurId);
buffer.ReleaseTemporaryRT(copyId);

// コマンドバッファをカメラに追加.
_camera.AddCommandBuffer(CameraEvent.AfterImageEffects, buffer);
```




## Requirement

* Unity5.5+ *(included Unity 2017.x)*
* No other SDK




## Usage

1. Download [StaticBluredScreen.unitypackage](https://github.com/mob-sakai/StaticBluredScreen/raw/master/StaticBluredScreen.unitypackage) and install to your project.
1. Attach `StaticBluredScreen` component to GameObject.
1. In playmode, call `StaticBluredScreen.UpdateTexture`.
1. Enjoy!

![image](https://user-images.githubusercontent.com/12690315/40522542-6fc60e1a-600c-11e8-8b22-9a41f21e8657.png)




## Demo

[demo](https://developer.cloud.unity3d.com/share/-kw9dNzDxX/webgl/)

ダイアログを開くタイミングで静的なスクリーンブラーを生成し、アニメーションでフェードイン/アウトするデモです.  
シーン内にカメラは1つしかありません.  
ダイアログが2つ重なったとき、更にブラーを生成しています.




## License

MIT  
© UTJ/UCL


## Author

[mob-sakai](https://github.com/mob-sakai)




## See Also

* GitHub Page : https://github.com/mob-sakai/StaticBluredScreen
* Issue tracker : https://github.com/mob-sakai/StaticBluredScreen/issues

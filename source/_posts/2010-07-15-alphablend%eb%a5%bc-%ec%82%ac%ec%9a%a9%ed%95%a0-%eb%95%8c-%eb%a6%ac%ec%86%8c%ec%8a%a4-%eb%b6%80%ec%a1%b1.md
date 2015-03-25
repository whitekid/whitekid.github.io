---
id: 281
title: AlphaBlend를 사용할 때 리소스 부족
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=281
permalink: /archives/281
dsq_thread_id:
  - 772598559
tags:
  - Delphi
---
[Dimmer][1]라는게 있습니다. 다이얼로그가 뜨면, 나머지를 흐리게 표시하는, 요즘 웹에서 많이 보이는 것이죠. 이것을 델파이로 구현한 것이 저 링크인데.. 오늘 사용하다보니 이상하게도 가려진 폼의 전체가 아닌 일부분만 조그만하게 표시되는 겁니다. 이상해서 Spy++로 봐도 윈도의 크기는 원하는데로 있는데, 표시만 저렇게 됩니다.

결국 AlphaBlend처리하는 소스를 보니 [SetLayeredWindowAttributes][2]를 사용하는데, 에게 실패하는 것 같아서 확인해보니 역시나입니다. 이게 리소스 부족이라는 메시지를 내면서 실패하더군요.

또한 이럴때 현상을 보면 DimmerForm이 작으면 제대로 되가다 일정 크기가 넘어가면 제대로된 동작을 하지 않던가, 아니면 폼에 일그러진 이미지들이 표시됩니다.

결국 SetLayeredWindowAttributes가 실패하면 Dimmer를 표시하지 않도록 변경했습니다.

    procedure TDimmerForm.Display;
    const
      cUseAlpha: array [Boolean] of Integer = (0, LWA_ALPHA);
      cUseColorKey: array [Boolean] of Integer = (0, LWA_COLORKEY);
    begin
      BoundsRect := Application.MainForm.BoundsRect;

      // 여기는 TCustom.SetLayeredAttribs에서 가져옮
      if SetLayeredWindowAttributes(Handle, ColorToRGB(TransparentColorValue), AlphaBlendValue,
        cUseAlpha[AlphaBlend] or cUseColorKey[TransparentColor]) then
        Show;

이젠 리소스가 부족하면 차라리 안보이지.. 엉뚱하게 보이지는 않습니다. 아.. 이 리소스 부족에러.. 어떤놈이 리소스를 많이 잡아먹는지 알 수 있거나, 아님 리소스 누스를 만들어 내는 프로그램들을 어떻게 찾을 수 있는 방법 없을까나...

 [1]: http://bit.ly/aFTtpy
 [2]: http://bit.ly/96oDXi
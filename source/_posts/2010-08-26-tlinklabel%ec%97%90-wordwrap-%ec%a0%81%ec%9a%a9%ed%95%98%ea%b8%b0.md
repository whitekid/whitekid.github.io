---
id: 342
title: TLinkLabel에 WordWrap 적용하기
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=342
permalink: /archives/342
dsq_thread_id:
  - 785328964
tags:
  - Delphi
  - MadeByKidd
---
TLinkLabel은 사용하기 따라서 상당히 유용합니다. 근디 WordWrap이 없군요. 이게 없으니깐 AutoSize를 줘도 한줄로 길게 쭈욱 나와버립니다. TLabel의 WordWrap과 같이 동작하면 참 편하겠는데 말이지요. 어찌 되었건 아래처럼 MaxWidth 변수가 사용되는 부분만 몇줄 추가하면 원하는데로 됩니다.

    procedure TnxLinkLabel.AdjustBounds;
    var
      DC: HDC;
      SaveFont: HFont;
      TextSize: TSize;
      Parsed: string;
      MaxWidth: Integer;
    begin
      if not (csLoading in ComponentState) and FAutoSize then
      begin
        if CheckWin32Version(6) and UseThemes then
        begin
          if HandleAllocated then
          begin
            if FWordWrap then
              MaxWidth := ClientWidth else
              MaxWidth := MaxInt;

            SendGetStructMessage(Handle, LM_GETIDEALSIZE, MaxWidth, TextSize);
            SetBounds(Left, Top, TextSize.cx + (GetSystemMetrics(SM_CXBORDER) * 4),
              TextSize.cy + (GetSystemMetrics(SM_CYBORDER) * 4));
          end;
        end
        else

TCustomLinkLabel.AdjustBounds를 단 몇 줄 수정하면 되는데, AdjustBounds가 virtual로만 되어있어도 편한데 그게 아니라서 이렇게 수정만 해서는 안됩니다. 그렇다고 vcl을 직접 다시 컴파일하기도 그렇고..

결국은 TCustomLinkLabel, TLinkLabel을 모두 몽땅 가져다가 새 이름으로 콤포넌트를 등록해버렸군요. 찝찝하지만 Go~
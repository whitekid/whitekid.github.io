---
id: 142
title: ListView의 SubItem에 ProgressBar 그리기
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=142
permalink: /archives/142
dsq_thread_id:
  - 717513165
tags:
  - Delphi
  - themes
  - TListView
---
TListView에 ProgressBar를 표현하는 것을 간단하게 생각하면 TProgressBar를 생성하고 SubItem위치에 표시하면 됩니다. 그리고 컬럼이 사이즈가 조절될 때 (HeaderTracking) 적당하게 위치 조절해주면 됩니다. 간단하게 심플하게 생각하면 그렇지요.

근데 그냥 OnDrawSubItem 이벤트에서 Themes을 이용해서 그려주면 어떨까? 하고 해 봤는데, 생각보다 간단합니다.

    procedure TThemeForm.ListView1CustomDrawSubItem(Sender: TCustomListView;
      Item: TListItem; SubItem: Integer; State: TCustomDrawState;
      var DefaultDraw: Boolean);
    var
      Element: TThemedElementDetails;
      R: TRect;
      Progress: 1..100;
    begin
      if ((Sender as TListView).ViewStyle = vsReport) and (SubItem = 3) then
      begin
        R := Item.SubItemRect(SubItem, drBounds);
        Inc(R.Left);
        if Sender.GridLines then Dec(R.Bottom);

        Element := ThemeServices.GetElementDetails(tpBar);
        ThemeServices.DrawElement(Sender.Canvas.Handle, Element, R);

        Element := ThemeServices.GetElementDetails(tpChunk);
        InflateRect(R, -4, -3);
        Progress := 30;
        R.Right := R.Left + MulDiv(Progress, R.Right - R.Left, 100);
        ThemeServices.DrawElement(Sender.Canvas.Handle, Element, R);

        DefaultDraw := False;
      end;
    end;

SubItemRect 함수는 다음과 같습니다.

    function TListItemHelper.SubItemRect(SubItemIndex: Integer; Code: TDisplayCode): TRect;
    const
      Codes: array[TDisplayCode] of Longint = (LVIR_BOUNDS, LVIR_ICON, LVIR_LABEL, LVIR_SELECTBOUNDS);
    begin
      ListView_GetSubItemRect(ListView.Handle, Index, SubItemIndex, Codes[Code], @Result);
    end;

자 잘나옵니까?

{% img /images/ListViewSubItemProgressBar-300x127.png %}

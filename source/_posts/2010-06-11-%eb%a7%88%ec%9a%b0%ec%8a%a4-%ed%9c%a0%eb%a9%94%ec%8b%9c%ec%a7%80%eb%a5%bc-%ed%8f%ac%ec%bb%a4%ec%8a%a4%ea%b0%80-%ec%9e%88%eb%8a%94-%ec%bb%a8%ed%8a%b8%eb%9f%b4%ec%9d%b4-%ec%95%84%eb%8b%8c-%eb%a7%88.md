---
id: 121
title: 마우스 휠메시지를 포커스가 있는 컨트럴이 아닌 마우스가 위치한 컨트럴로 보내기
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=121
permalink: /archives/121
dsq_thread_id:
  - 750509650
tags:
  - Delphi
---
    {
      마우스 휠을 움직이면 포커스가 있는 컨트럴에 WM_MOUSEWHEEL이 가는데, 다른
      Application을 보면 마우스 포인터가 위치한 컨트럴에서 반응을 하는 경우가 있다.

      이 유닛을 포함하면 WM_MOUSEWHEEL 메시지가 마우스 커서가 있는 컨트럴로 간다.
      따라서 마우스 클릭을 하여 포커스를 이동할 필요가 없다.
    }
    unit WMMouseWheelUnderCursor;

    interface

    implementation

    uses
      Windows, Forms, Classes, AppEvnts, Messages, Controls;

    type
      TWMMouseWheelMessageUnderCursor = class(TApplicationEvents)
      private
        procedure OnMessageHandler(var Msg: tagMSG; var Handled: Boolean);

      public
        constructor Create(AOwner: TComponent); override;
    end;

    { TWMMouseWheelMessageUnderCursor }

    constructor TWMMouseWheelMessageUnderCursor.Create(AOwner: TComponent);
    begin
      inherited;

      OnMessage := OnMessageHandler;
    end;

    procedure TWMMouseWheelMessageUnderCursor.OnMessageHandler(var Msg: tagMSG;
      var Handled: Boolean);
    var
      Pt: TPoint;
      Control: TWinControl;
     begin
      if Msg.message = WM_MOUSEWHEEL then
      begin
        Pt.X := Word(Msg.lParam);
        Pt.Y := HiWord(Msg.lParam);
        Control := FindVCLWindow(Pt);
        if not Assigned(Control) then
          Handled := True
        else if Control.Handle <> Msg.hwnd then
        begin
          Handled := True;
          SendMessage(Control.Handle, WM_MOUSEWHEEL, Msg.wParam, Msg.lParam);
        end;
      end;
    end;

    initialization
      TWMMouseWheelMessageUnderCursor.Create(Application);
    end.
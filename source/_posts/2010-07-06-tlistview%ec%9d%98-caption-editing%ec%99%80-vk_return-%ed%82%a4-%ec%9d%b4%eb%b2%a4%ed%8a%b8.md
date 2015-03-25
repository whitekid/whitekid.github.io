---
id: 171
title: TListView의 Caption Editing와 VK_RETURN 키 이벤트
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=171
permalink: /archives/171
dsq_thread_id:
  - 780662835
tags:
  - Delphi
  - TListView
---
제목을 쓰기 거시기해서 위처럼 적었고.. 하여간 TListView에서 EditCaption 기능은 자주 사용되는 기능이죠.  그리고 리스트 뷰에서 Enter를 누르면 뭔가 실행하는 것도 자주 하는 것이고..

이 두가지를 하게 되면 뭔가 꼬입니다. TListView의 Key[Up|Down]에서 VK_RETURN일 때 뭔가 하려는데, 잘 되는것 처럼 보이는데, 캡션을 편집중일 떄(IsEditing=True)는 캡션 편집을 완료하고자 누르는 엔터에 반응을 하는 문제가 있습니다.

    case Key of
      VK_RETURN:
          // Editing을 끝내는 Enter도 아래가  수행됩니다.
          DoSomething;

이걸 해결하고자 간단히 IsEditing을 사용하면..

    case Key of
      VK_RETURN:
          // 이렇게해도 상황은 나아지지 않지요.
          if not IsEditing then
            DoSomething;

잘 될것 같은데, 안됩니다. 저 이벤트는 Inline Editor의 Enter이벤트를 넘기는 것이니깐, 저 함수가 실행될 때도 IsEditing=True가 됩니다. 이걸 해결하려면... 뭐 TListView의 Notify이벤트를 써야하니 TListView를 상속해서 하나 만들어야 하지요.

    TMyListView = class(TListView)
    private
      FItemCaptionEditing: Boolean;
    protected
        procedure CNNotify(var Message: TWMNotify); message CN_NOTIFY;
        procedure KeyUp(var Key: Word; Shift: TShiftState); override;
    end;

    procedure TMyListView.CNNotify(var Message: TWMNotify);
    begin
      inherited;

      with Message do
        case NMHdr^.code of
          LVN_BEGINLABELEDIT: FItemCaptionEditing := True;
          LVN_ENDLABELEDIT:  FItemCaptionEditing := False;
        end;
    end;

    procedure TMyListView.KeyUp(var Key: Word; Shift: TShiftState);
    begin
      case Key of
        VK_RETURN:
          if not FItemCaptionEditing then
            DoSomething else
            inherited;
      end;
    end;

해결되었군요. 근데 이게 델파이의 문제일까요? Windows의 문제일까요? Windows에 한표..
근데 문제일까요? 아닐까요?... 흠흠.. 그건... 에매하네요.
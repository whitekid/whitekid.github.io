---
id: 191
title: TPopupMenu의 기본 메뉴 실행하기
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=191
permalink: /archives/191
dsq_thread_id:
  - 790720208
categories:
  - Uncategorized
tags:
  - Delphi
---
[<img class="alignright size-medium wp-image-192" title="popup_default_item" src="http://blog.woosum.net/wp-content/uploads/2010/07/popup_default_item-177x300.png" alt="" width="177" height="300" />][1]윈도우 탐색기의 팝업 메뉴를 보면 오른쪽 그림처럼 Open 메뉴 아이템은 굵은 폰트로 표시됩니다. 이건 해당 아이템을 더블클릭 또는 엔터키를 누르면 해당 메뉴를 실행하겠다는 Windows의 표현입니다.

당연히 TPopupMenu에서도 지원하지요. TMenuItem.Default 프러퍼티가 있으면 수행하면 됩니다.

그런데 이 기능을 구현하려면 여간 귀찮치요.

  1. 키보드 메시지 처리해서 VK_RETURN 메시지 처리하고
  2. 더블클릭했을때도 체크하고
  3. 또한 해당 컨트럴에 연결된 팝업 메뉴도 확인해서 기본 메뉴가 있는지 없는지 확인하고..
  4. 있으면 이벤트 실행하고..

컨트럴에 연결된 팝업 메뉴의 기본 액션을 수행하는 것만 보자면..

[code lang="delphi"]  
function TwsControlHelper.ExecuteDefaultPopupAction: Boolean;  
// 팝업 메뉴의 디폴트로 설정된 메뉴 아이템에 설정된 이벤트를 실행한다  
var  
Item, DefaultItem: TMenuItem;  
begin  
Result := False;  
if not Assigned(PopupMenu) then Exit;

DefaultItem := nil;  
for Item in PopupMenu.Items do  
if Item.Default then  
begin  
DefaultItem := Item;  
Break;  
end;

if Assigned(DefaultItem) then  
begin  
if Assigned(DefaultItem.Action) then  
Result := DefaultItem.Action.Execute  
else if Assigned(DefaultItem.OnClick) then  
begin  
Result := True;  
DefaultItem.OnClick(DefaultItem);  
end;  
end  
end;  
[/code]

TControl의 Helper로 작성했이니 어디서든 저렇게 쓰면 됩니다. 이것을 키보드나 마우스 이벤트 핸들러에 적용해보면..

[code lang="delphi"]  
procedure TwsListView.DblClick;  
begin  
// 더블클릭에 아무런 이벤트도 연결되어 있지않으면 기본 팝업 액션을 수행  
if not Assigned(OnDblClick) and Assigned(PopupMenu) then  
ExecuteDefaultPopupAction else  
inherited;  
end;

procedure TwsListView.KeyUp(var Key: Word; Shift: TShiftState);  
begin  
case Key of  
VK_RETURN:  
if not FCaptionEditing then  
begin  
if not ExecuteDefaultPopupAction then  
inherited;  
end else  
inherited;  
else inherited;  
end;  
end;  
[/code]

이렇게 됩니다.

TwsListView에 연결된 팝업메뉴에 기본 메뉴가 있으면, 이제 리스트 뷰에서 Enter를 누르거나 아이템을 더블클릭하면 해당 메뉴 이벤트가 자동으로 수행됩니다.

 [1]: http://blog.woosum.net/wp-content/uploads/2010/07/popup_default_item.png
---
id: 586
title: 'VirtualTree updated to 4.8.7 ... 그리고..'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=586
permalink: /archives/586
dsq_thread_id:
  - 720795295
categories:
  - Uncategorized
tags:
  - Delphi
  - VirtualTree
---
오늘 보니 [VirtualTree가 4.8.7로 10월 말에 업데이트][1]{.broken_link} 되었네요. 그래서 가차없이 업데이트 했는데  TVirtualStringTree를 를 그대로 쓰지않고 몇 가지 수정을 가해서 사용하고 있던 부분에서 오류가 발생합니다.

## DoHeaderClick의 파라미터가 달라지고 더이상 virtual이 아니다.

[code lang="delphi"]  
procedure DoHeaderClick(HitInfo: TVTHeaderHitInfo); virtual;  
procedure DoHeaderClick(Column: TColumnIndex; Button: TMouseButton; Shift: TShiftState; X, Y: Integer);  
[/code]</pre> 

파라미터 달라진 것은 이해하겠습니다. 그런데 virtual 이었던 것이 virtual이 아닌것으로 돌아가다니.. 다른 DoXXX 함수들은 virtual인것을 보니 아마도 실수를 한 듯 합니다.

결국은 DoHeaderMouseUp을 override 하는 것으로 처리했지요

[code lang="delphi"]  
procedure TVirtualStringTreeEx.DoHeaderMouseUp(Button: TMouseButton;  
Shift: TShiftState; X, Y: Integer);  
var  
Column: Integer;  
begin  
inherited;

// Convert vertical position to local coordinates.  
Inc(Y, Header.Height);  
Column := Header.Columns.ColumnFromPosition(Point(X, Y));

DoSometing;  
end;  
[/code]

 [1]: http://www.soft-gems.net/index.php?option=com_content&task=view&id=59&Itemid=1
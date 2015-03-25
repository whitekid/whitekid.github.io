---
id: 164
title: TValueListEditor OnValidate
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=164
permalink: /archives/164
dsq_thread_id:
  - 722057278
categories:
  - Uncategorized
tags:
  - Delphi
---
TValueListEditor에서 입력한 값을 Validation하려고 봤더니 Validate에 뭔가 이상합니다. OnValidate 이벤트에 성공/ 실패가 있을줄 알았더니만 없네..

그래서 OnValidate에 아래처럼하고

    procedure TValueListEditorForm.ValueListEditor1Validate(Sender: TObject; ACol,  
      ARow: Integer; const KeyName, KeyValue: string);  
    begin  
      if Trim(KeyValue) = " then  
        FValidateFailed := True;  
      end;  

선택을 못하게.. ㅋㅋ

    procedure TValueListEditorForm.ValueListEditor1SelectCell(Sender: TObject; ACol,  
      ARow: Integer; var CanSelect: Boolean);  
    begin  
      if FValidateFailed then  
      begin  
        CanSelect := False;  
        FValidateFailed := False;  
      end;  
    end;  

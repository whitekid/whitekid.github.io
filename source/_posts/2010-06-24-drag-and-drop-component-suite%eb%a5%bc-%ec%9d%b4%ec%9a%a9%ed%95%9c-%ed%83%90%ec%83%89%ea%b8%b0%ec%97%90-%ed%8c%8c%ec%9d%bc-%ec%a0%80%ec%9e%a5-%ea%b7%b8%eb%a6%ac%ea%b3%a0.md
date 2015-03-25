---
id: 161
title: Drag and Drop Component Suite를 이용한 탐색기에 파일 저장 그리고..
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=161
permalink: /archives/161
dsq_thread_id:
  - 752162935
tags:
  - Delphi
  - 'Drag&amp;Drop'
---
[Drag and Drop Component Suite][1]를 이용해 어제 오늘 놀고 있습니다. 이걸로

  1. ListView에 파일 목록이 있고 이를 탐색기로 Drag&Drop로 저장
  2. 파일의 데이터는 실제 파일이 아니고 Application에서 생성하는 데이터
  3. Drag&Drop으로 ListView안에서 위치 조절

와 같은 일을 하고자 합니다. 1, 2 번은 Drag&Drop Suite에서 제공해주는 TVirtualFileStreamDataFormat을 이용해서 하면 되는데, 마지막 리스트 안에서 위치조절하는 것은 복잡하네요.

Shell로 Drag&Drop이 없으면 간단히 델파이에서 제공하는 기능을 이용하면 되는데, Drag&Drop Suite와 같이 쓰면 이것이 안됩니다. 둘중에 하나만 사용해야하는 현상이 발생합니다(이것때문에 삽질좀 하고..).

결국은 TCustomDataFormat을 이용해서 2가지 데이터 형태를 같이 사용해서 해결했습니다. 자세한 내용은 코드확인!

[DragDropShellAndVCL][2]

 [1]: http://melander.dk/delphi/dragdrop

---
id: 692
title: 'Django: request.user에 나만의 User를 넣기?'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=692
permalink: /archives/692
dsq_thread_id:
  - 736055365
categories:
  - Uncategorized
tags:
  - django
---
음.. 제목이 이상하긴 한데... django의 authentication을 사용하면 request.user에 django.contrib.auth.models.User 객체가 생긴다. 그런데 말이다 처음부터 django를 사용할 경우는 이거 쓰면 되는데, 이미 돌아가는 프로젝트를 django로 옮겨가는 경우 django의 user가 맞지 않는게 있다.

first\_name, last\_name 등은 어쩌면 우리나라의 환경에 맞지 않는 것일 수 도 있고, username 보다는 userid가 더 알아먹기 편하니 말이다.. 물론 이미 기존 데이터베이스도 그렇게 되어있을 것이고...

이런 상황에서 request를 처리할때 django의 스타일과 다르게 user를 가져오는 것도.. 솔직히 맘에 들지 않는다. 남이 고민고민해서 만들어 놓은 것은 되도록이면 그들의 철학에 맞게 쓰자는게 나의 주의니깐...

그래서 고심끝에 request.user를 사용하되 request.user가 django가 지원하는 것이 아닌 나만의 custom user 모델로 생성하고, 그리고 몇몇 django에서 필요한 메소드의 함수만 만들어 주면 될 것이라 생각하고 작업했더니 아주 나이스하게 돌아긴다..~~

django.user를 설정하는 곳은 middleware이니 나도 새로운 middleware를 만들어서 처리했다. 물론 django authentication의 코드를 좀 참고했다. 대충 아래 코드 보시고...

[code lang="python"]  
from myapp.models import AnonymousUser, User

class LazyUser(object):  
def \_\_get\_\_(self, request, obj_type = None):  
if not hasattr(request, '__user'):  
request._\_user = User.objects.get(id=request.session['user\_id']) if request.session.get('user_id', False) else AnonymousUser()  
return request.__user

class AuthenticationMiddleware:  
def process_request(self, request):  
if not hasattr(request.\_\_class\_\_, 'user') or not request.\_\_class\_\_.user is LazyUser:  
request.\_\_class\_\_.user = LazyUser()  
[/code]

이렇게하면 이제 request.user를 우리만의 user객체로 사용가능하니.. 좋다~ 

실제 작성했던 코드에서 대충 개념만 표현한 것이라 제대로 동작할 지는 나도 모른다.. ㅋㅋ
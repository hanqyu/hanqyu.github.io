---
title: drf GenericAPIView에서 lookup_field의 역할
categories: [TIL]
tags: [django, Django-rest-framework]
excerpt: GenericAPIView 를 이용해서 Update API 를 만들고 싶었는데 (post로만 요청이 날아오는 걸로 정해져있기 때문에 UpdateAPIView를 안씀)
---

# 맥락

GenericAPIView 를 이용해서 Update API 를 만들고 싶었는데 
(post로만 요청이 날아오는 걸로 정해져있기 때문에 UpdateAPIView를 안씀)

update할 대상이 되는 object를 구해야 했었음

# What I learned

이를 instance attribute에 **lookup_field**로 추가하면 구현이 되는 줄 알았는데, 사실은 queryset을 만들어서 활용해야 했음. **lookup_field**의 역할은 detail view에 해당되는 건데 만약 lookup_field를 username으로 바꾼다면 `user/1/`이 아닌 `user/hanqyu/`의 update를 바로 실행할 수가 있는 것.

- 그도 그럴 것이 **lookup_field**의 default는 **'pk'**임

        class GenericAPIView(views.APIView):
        	# If you want to use object lookups other than pk, set 'lookup_field'.
        	# For more complex lookup requirements override `get_object()`.
        	lookup_field = 'pk'
        	lookup_url_kwarg = None

# 출처

[https://www.reddit.com/r/djangolearning/comments/4ciioy/username_instead_of_id_for_urls_in_django_rest/](https://www.reddit.com/r/djangolearning/comments/4ciioy/username_instead_of_id_for_urls_in_django_rest/)
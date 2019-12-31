---
title: 개발인턴경험기 - 1. TDD의 중요성은 알겠는데, 어떻게 하는거지?
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories: [Blog]
tags: [TDD, RubyOnRails, 개발환경]
excerpt: 첫 페어 프로그래밍을 했을때, 가장 먼저한 작업이 테스트 셋팅이었다.
header:
 teaser: assets/images/teasers/tdd-test-passed.png
---

> 첫 페어 프로그래밍을 했을때, 가장 먼저한 작업이 테스트 셋팅이었다.

CTO님은 테스트 셋팅을 하면서 TDD의 중요성에 대해 설명해주셨다. 심지어 몇몇 대기업은 인사고과를 평가할 때 작성한 코드에 테스트를 거치는 line*(테스트 커버리지, test coverage)*이 몇 퍼센트인지를 고려한다고 한다. 일반적으로 95~98%는 테스팅이 거쳐가야 한다고.

내가 다녔던 회사는 rails를 쓰고 있었고, rails에 잘 맞는 테스팅 라이브러리는 **spec**이라고 했다. spec에 달려있는 Factory는 테스팅을 위한 임의의 모델 레코드를 생성해주는 역할을 했고, 모든 controller의 spec은 미리 만들어놓은 Factory를 상속하도록 해서 테스트 케이스를 새로 만들때마다 User와 같이 공통적으로 쓰이는 모델의 레코드를 자동으로 생성하도록 할 수 있다.

> TDD를 위해선 그에 맞는 환경이 필요하다

일반적으로 development → staging → production의 서비스 환경이 존재하는 건 알았지만, 그리고 입사해서는 이 환경이 test → development → staging → production의 4가지 환경으로 되어 있었다. 그리고 test 환경이 뭘 위해 있는지 구체적으로 이해하진 못했다.

**그냥 test만 하는 환경이라면 development 환경이라면 충분하지 않은 것일까?**

일반적으로 development는 그래도 서비스의 전체적인 기능이 갖추어져 있음을 전제로 한다. 그래서 내가 개발한 기능을 넣었을때 다른 기능과 조화롭게 잘 돌아가는지 *육안으로* 체크하는 것이다. staging은 이 단계에서 더 나아가서 실제 데이터에 가까운(이상적으로는 실제 데이터 그 자체) 환경에 놓였을 때 문제없이 작동하는지 체크하기 위해 필요하다.

반면 test 환경은 내가 개발하는 기능만 떼어놓아 테스팅하기 적합한 환경이다. 그래서 test 환경에 DB에 들어가보면 아무 데이터도 남아있지 않다. 내가 개발하는 기능 이외에 다른 기능의 작동 여부를 염두에 두지 않기 때문에 DB가 채워져 있을 필요가 없다.

> testing의 작동방식

위에서 Factory나 spec에서 잠깐 언급했지만 testing의 작동 방식은 아래와 같다.

1. **testing에 필요한 model object를 생성한다.**
2. 테스트하고자 하는 기능을 호출한다.
3. 테스트 결과로 기대되는 값을 비교한 후 성공/실패 여부를 반환한다.
4. **테스트를 위해 생성했던 model object를 삭제한다.**

2번과 3번의 과정은 일반적으로 예측할 수 있는 과정이지만, 1,4번의 과정은 새롭게 알게 되어 좋았다. 앞서 말한 이유로 test 환경은 DB가 비워져있다. 그래서 비워져 있는 상태를 유지하기 위해 4번의 object를 삭제하는 과정이 필요하다. 내가 접했던 spec은 이 삭제하는 과정을 자동으로 진행해주었다. 또한 Jetbrain 계열의 에디터는 이 테스팅 기능을 에디터 내에서 잘 지원해주는 것도 좋았다.

1번의 object를 생성하는 과정도 어렵지 않다. 미리 valid한 object라고 생각되는 데이터를 정의해두고 Factory에 넣어두면 된다. 새로운 테스트 케이스를 생성할때마다 이 FactoryBot을 상속해주면 1번과 4번의 생성, 삭제의 과정은 더 이상 관리해줄 필요가 없다.

2번과 3번은 어떤 테스팅 툴을 사용하느냐에 따라 다르다. spec의 예시를 보자면 다음과 같다.

```ruby
    RSpec.describe CategoriesController, type: :controller do
      login_super_admin_user
    
      it 'shows index' do  # 테스트 제목 (사람이 구별하기 위함)
        get :index  # index view를 get 메소드로 요청
        expect(response.status).to render_template(:index)  # index template이 렌더링 되면 테스트 성공
      end
    
      it 'shows category' do  # 테스트 제목
        category = FactoryBot.create(:category)  # category 데이터를 만들고 (Factory)
        get :show, params: {id: category.id}  # show view를 get 메소드로 요청. id 파라미터에는 방금 생선한 category의 id를 넣어서 요청
        expect(response.status).to render_template(:show)  # show template이 렌더링 되면 성
      end
    end
```

`login_super_admin_user` : 이미 정의하고 있는 super admin user로 로그인

`shows index`라는  테스트 케이스에서는

1. `:index` view를 GET 메소드로 호출했을때,
2. `response.status`가 true인지 체크하는 로직이다. 
(`render_template(:index)`가 잘 되었다면 true를 반환함)

즉, 잘 렌더링되었다면 success, 아니면 fail을 반환한다.

마찬가지로 `shows category`의 케이스에서는 rendering의 결과를 테스트하고 있는데, 필요한 category object를 FactoryBot을 이용해 생성한다. 특정한 파라미터를 전달하지 않는 이유는 이미 FactoryBot에 category의 디폴트 값으로 생성할 데이터를 지정해뒀기 때문이다.

또한 위처럼 여러 케이스를 한꺼번에 넣어두고 순차적으로 테스트하도록 할 수도 있어서 한 기능의 여러 케이스를 체크하기도 편리하다.

다음에는 내가 공부하고 있는 Django에서 테스팅 환경 셋팅, 적용, 사례를 보여주고자 한다.

<!--TODO Django 테스팅 환경-->
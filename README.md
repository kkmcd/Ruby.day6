# Day6

## 1. Controller

### 1) Role

- 서비스 로직을 가지고 있음
- 그 동안 `app.rb`에서 작성했던 모든 내용이 `Controller`에 들어감
- `Controller`는 하나의 서비스에 대해서만 관련한다.
  - 단순히 생각해서 게시판에 관련된 하나 만들고, 유저가 될 수도있고.. 하나에 대해서만 짜는게 원칙
- `Controller`를 만들 때에는 `$rails g(g는 generate) controller 컨트롤러명` 을 이용한다.

```rails
$ rails g controller home
# app/controllers/home_controller.rb 파일 생성

kkmcd:~/test_app $ rails g controller home
Running via Spring preloader in process 1721
      create  app/controllers/home_controller.rb
      invoke  erb
      create    app/views/home
      invoke  test_unit
      create    test/controllers/home_controller_test.rb
      invoke  helper
      create    app/helpers/home_helper.rb
      invoke    test_unit
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/home.coffee
      invoke    scss
      create      app/assets/stylesheets/home.scss
```



### 2) Basic

#### (1) *app/controllers/home_controller.rb*

```ruby
class HomeController < ApplicationController
   #상단의 코드는 ApplicationController를 상속받는 코드 
end
```

※scss 파일은 css 파일과 거의 동일하다고 보면됨.

- `HomeController`를 만들면 *app/views* 파위에 컨트롤러 명과 일치하는 폴더가 생긴다.
- `HomeController`에서 액션(def)을 작성하면 해당 액션명과 일치하는 `view`파일을 *app/views/home* 폴더 밑에 작성한다.
- 사용자의 요청을 받는 url설정은 *config/routes.rb*에서 한다.

> Rails에는 Development, Test, Production 환경(모드)가 있다. 
>
> Development 환경에서는 변경사항이 자동적으로 확인되고, 모든 로그가 찍힌다.
>
> Production 환경에서는 변경사항도 자동적으로 저장되지 않고, 로그도 일부만 찍힌다. `$ rails s`로 서버를 실행하지 않는다.

#### (2) test_app > config > routes.rb

```ruby
Rails.application.routes.draw do
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
  get '/home/index' => 'home#index' #/로 시작하는 규칙만 지켜주면됨.
  root 'home#index' # 맨 처음 시작화면을 지정함.
  post '/lotto' => 'home#lotto'
end
```

#### (3) views/home/index.html.erb

```ruby
<h1><%= @msg %></h1>
```

#### (4) test_app/app/controllers/home_controller

```ruby
class HomeController < ApplicationController
    def index
       @msg = "나의 첫 레일즈 앱에 오신것을 환영합니다" 
    end
end
```

#### (5) 시작하기(아래 명령어 작성 이후에 메뉴창의 run project를 클릭해서 url 주소를 받음)

```ruby
$ rails s -p $PORT -b $IP
```



### 3) Making lotto

#### (1)routes.rb

```ruby
Rails.application.routes.draw do
	...
  get '/lotto' => 'home#lotto'
end
```

#### (2) home controller

```ruby
class HomeController < ApplicationController
	...   
    def lotto
       @lotto = (1..45).to_a.sample(6).sort!
    end
end

```

#### (3) lotto.html.erb

```ruby
<h1>이번주 추천 번호는 <%= @lotto %> 입니다.</h1>
```



### 4) 간단 도전 과제

- 점심메뉴를 랜덤으로 보여준다.
- 글자+이미지가 출력된다.
- 점심메뉴를 저장하는 변수는 `Hash` 타입으로 한다.
  - {"점심메뉴 이름" => "https://... .jpg"}
  - `Hash`에서 모든 key 값을 가져오는 메소드는 `.keys`이다.
- 요청은 `/lunch`로 받는다.

#### (1) home_controller

```ruby
class HomeController < ApplicationController
    ...
    def lunch
       @menu = {"순남시래기" => "https://t1.daumcdn.net/cfile/tistory/2110194F561E2E7B30", "20층" => "https://t1.daumcdn.net/cfile/tistory/27234D3F58F61AF006" }
       @lunch = @menu.keys.sample
    end
end
```

#### (2) routes.rb

```ruby
Rails.application.routes.draw do
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
	...
  get '/lunch' => 'home#lunch'
end
```

#### (3) lunch.html.erb

```ruby
<h1>오늘은 메뉴는 <%=@lunch%> 입니다.</h1>
<img src = "<%=@menu[@lunch]%>" ></a>
```



## 2. Model

```ruby
$ rails g model 모델명
```

=> model > user.rb 생성 + db > migrate > 2018061402104... 생성

#### (1) db > migrate > 2018061402104

```ruby
class CreateUsers < ActiveRecord::Migration[5.0]
  def change
    create_table :users do |t|
        
	 t.string "user_name"
      t.string "password"
      t.string "email"  
      t.timestamps
    end
  end
end
```

#### (2) db 생성(위의 파일을 수정하면 아래의 명령어로 만들면 됨.)

```ruby
rake db:migrate
```

#### (3) app>models>user.rb

```ruby
class User < ApplicationRecord
    
end
```

#### (4) 테이블에 작업하기

- Rails는 ORM(Object Relation Mapper)을 기본적으로 장착하고 있음(Active Record)
- migrate 파일을 이용해서 DB의 구조를 잡아주고 명령어를 통해 실제 DB를 생성/변경한다.
- Model 파일을 이용해서 DB에 있는 자료를 조작함

```ruby
kkmcd:~/test_app $ rails c
2.3.4 :005 > u1 = User.new # 빈 껍데기(테이블에서 row 한 줄)를 만든다.
2.3.4 :006 > u1.user_name = "haha" # 자료조작
2.3.4 :007 > u1.password = "1234"
2.3.4 :008 > u1.save # 실제 DB에 반영(저장)
2.3.4 :009 > u1.password = "4321"
2.3.4 :010 > u1.save
```

#### (5) 테이블 수정

```ruby 
#실제 DB에 스키마 파일대로 적용하기
$ rake db:migration
#테이블을 수정할 때는, drop시키고 다시 만들어야한다.
$ rake db:drop
$ rake db:migration
즉, DB 구조를 수정했을 경우 drop을 통해 DB를 날린다음 다시 migrate 해준다.
```



### 2) 회원등록 창 만들기

user 에 index.html.erb / new.html.erb / show.html.erb 를 만든다.

rails g controller user 로 controller를 만든다.



#### (1) route.erb

```ruby
Rails.application.routes.draw do
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
  get '/home/index' => 'home#index' #/로 시작하는 규칙만 지켜주면됨.
  
  #route => controller 순서임.
  
  root 'home#index'
  get '/lotto' => 'home#lotto'
  get '/lunch' => 'home#lunch'
  
  get '/users' => 'user#index'
  get '/user/:id' => 'user#show'
  get '/users/new' => 'user#new'
  post '/user/create' => 'user#create'
  
end
```

#### (2) user_controller.rb

```ruby
$ rails g controller user 하면 user_controller.rb가 app > controllers에 생성됨
```

```ruby
class UserController < ApplicationController
    def index 
        @users = User.all #테이블의 모든 정보를 조회해서 @users에 담음
    end
    
    def new

    end

    def create
        u1 = User.new
        u1.user_name = params[:name]
        u1.password = params[:password]
        u1.save
        redirect_to "/user/#{u1.id}"
    end
    
    def show
        @user = User.find(params[:id])
    end
end
```

#### (3) index.html.erb

```ruby
<ul>
    <% @users.each do |user| %>
    <li><%= user.user_name %></li>
    <%end%>
</ul>
<a href="/users/new">새 회원등록</a>
```

#### (4) new.html.erb

```ruby
<form action="/user/create" method="POST">
    <input type="hidden" name="authenticity_token" value="<%= form_authenticity_token %>">
    <input type="text" name="user_name" placeholder = "회원이름">
    <input type="password" name="password" placeholder = "비밀번호">
    <input type="submit" value="등록하기">
</form>
```

#### (5) show.html.erb

```ruby
<h1><%= @user.user_name %></h1>
<p><%= @user.password %></p>
<a href="/users"> 전체유저보기</a>
```



액션명과 view의 파일명이 같아야한다.!

#### ※우리가 만들어놓은 모든 route를 조회하는 방법이 있음.

- rake route
- User.all
- User.first
- route에서 users/new가 아니라 user/new를 쓰고싶다면 user/new를 위로 올리면 됨



### 간단과제

- 그 동안에 뽑혔던 내역을 저장해주는 로또번호 추천기
- `/lotto` => 새로 추천받은 번호를 출력
  - `a`태그를 이용해서 새로운 번호를 발급
  - 새로 발급된 번호가 가장 마지막과 최 상단에 같이 뜬다.
  - 최 상단의 메시지는 `이번주 로또 번호는 [...] 입니다`
- `/lotto/new` => 신규 번호를 발급, 저장 후 `/lotto`로 리디렉션
- 모델명 : Lotto
- 컨트롤러명 : LottoController



#### (1) model 생성

```ruby
$ rails g model lotto
```

#### (2) lotto_controller.rb

```ruby
class LottoController < ApplicationController
    def index
       @new_number = Lotto.last
       @numbers = Lotto.all
    end
    
    def new
        number = (1..45).to_a.sample(6).sort.to_s
        lotto = Lotto.new
        lotto.numbers = number
        lotto.save
        redirect_to '/lotto'
    end
end
```

#### (3) routes.rb

```ruby
Rails.application.routes.draw do
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
	...
  get '/lotto' => 'lotto#index'
  get '/lotto/new' => 'lotto#new'
  
end
```

#### (4) index.html.erb

```ruby
<p>이번주 추천 숫자는 <%= @new_number.numbers %> 입니다.</p>
<a href="/lotto/new">새 번호 발급받기</a>
<ul>
    <% @numbers.each do |number| %>
        <li><%= number.numbers %></li>
    <%end%>
</ul>
```

#### (5) db > migrate > 20180614052947...

```ruby
class CreateLottos < ActiveRecord::Migration[5.0]
  def change
    create_table :lottos do |t|
      t.string "numbers"
      t.timestamps
    end
  end
end
```


# How to build a Reddit or Hacker News Style Web App in Rails 4 （文字版）


----------


<h2>创建新的专案：Radiit</h2>

`cd 12_in_12_challenge/demo`
`rails new radiit`
`cd raddit`
`rails s`

http://localhost:3000

![Alt text](http://ojjhaj1wp.bkt.clouddn.com/2017-09-11-024013.jpg)

`git init`
`git status`
`git add .`
`git commit -am "Initial commit"`


##增加link的框架：Generate Link scaffold

`git checkout -b link_scaffold`

`rails g scaffold link title:string url:string`
`rails s`

http://localhost:3000/links

保存

`git status`
`git add .`
`git commit -am "generate link scaffold"`

合并分支

`git checkout master`
`git merge link_scaffold`


<h2>增加用户登陆系统：Add devise and users model</h2>


`git checkout -b Add_devise_and_users_model`

```
gem 'devise'
```

`rails g devise:install`

修改文件 app/config/enviroments/development.rb

```rb
+	config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }

end
```

修改文件 app/views/layous/application.html.erb

```erb
<body>

+	<% flash.each do |name, msg| %>
+		<%= content_tag(:div, msg, class: "alert alert-#{name}") %>
+	<% end %>

	<%= yield %>
</body>
```

`rails g devise:views`

`rails g devise Users`

`rake db:migrate`

http://localhost:3000/users/sign_up

注册一个用户

`rails c`
`User.count`
`@user = User.first`

退出
`exit`

`rails status`
`rails add .`
`rails commit -m "Add devise and user model"`

修改文件 application.html.erb

```erb
<% if user_signed_in? %>
      <ul>
        <li><%= link_to 'Submit link', new_link_path %></li>
        <li><%= link_to 'Account', edit_user_registration_path %></li>
        <li><%= link_to 'Sign out', destroy_user_session_path, :method => :delete %></li>
      </ul>
    <% else %>
      <ul>
        <li><%= link_to 'sign up', new_user_registration_path %></li>
        <li><%= link_to 'sign in', new_user_session_path %></li>
      </ul>
    <% end %>
    <% yield %>
```

`rails g migration add_user_id_to_links user_id:integer:index`

`rake db:migrate`

`rails c`

`link`
`link.connection`
`link`

`git status`
`git add .`
`git commit -am "Add association between link and User"`

##授权许可：Authorization on links

`git checkout -b Authorization on links`

修改文件 link_controller.rb

```rb
def new
	@link = current_user.links.build
end

def create
	@link = current_user.links.build(link_params)
...
end
```

`rails c`
`@link = Link.last`
`@link.user`
`@link.user.email`

修改文件 links.controller

```rb
before_action :authenticate_user!, except: [:index, :show]
```

修改文件 views/links/index.html.erb

```erb
<% if link.user == current_user %>
	<td><%= link_to 'Edit', edit_link_path(link) %></td>
	<td><%= link_to 'Destroy', link, method: :delete, data: { confirm: 'Are you sure?' } %></td>
<% end %>
```

`rails c`
`@user = User.first`
`@link = Link.first`
`@link.user = User.first`
`@link`
`@link = Link.find(2)`
`@link.user = User.first`
`@link.save`
`@link = Link.first`
`@link.user = User.first`
`@link.save`

修改文件 views/links/links/index.html.erb

```erb
-	<br>
-	<%= link_to 'New Link', new_link_path %>
```

`git status`
`git add .`
`git commit -m "Authorization on links"`

`git checkout master`
`git merge add_users`

<h2>添加结构和基本样式：Add structure and basic styling</h2>

`git checkout -b add_bootstrap`

修改文件 Gemfile

```rb
+ gem 'bootstrap-sass'
```

`bundle install`

`mv app/assets/stylesheets/application.css app/assets/stylesheets/application.scss`

修改文件 app/assets/stylesheets/application.scss

```scss
+	@import "bootstrap-sprockets";
+	@import "bootstrap";
```

修改文件 application.js

```js
+	//= require bootstrap-sprochets
```

删除文件 app/assets/stylesheets/scaffold.scss

刷新页面

修改文件 views/layouts/application.html.erb

```erb
<!DOCTYPE html>
<html>
<head>
  <title>Reddit</title>
  <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track' => true %>
  <%= javascript_include_tag 'application', 'data-turbolinks-track' => true %>
  <%= csrf_meta_tags %>
</head>
<body>
  <header class="navbar navbar-default" role="navigation">
    <div class='navbar-inner'
      <div class="container">
        <div id="logo" class="navbar-brand"><%= link_to "Reddit", root_path %></div>
        <nav class="collapse navbar-collapse navbar-ex1-collapse">
          <% if user_signed_in? %>
            <ul class="nav navbar-nav pull-right">
              <li><%= link_to 'Submit link', new_link_path %></li>
              <li><%= link_to 'Account', edit_user_registration_path %></li>
              <li><%= link_to 'Sign out', destroy_user_session_path, :method => :delete %></li>
            </ul>
          <% else %>
            <ul class="nav navbar-nav pull-right">
              <li><%= link_to 'Sign up', new_user_registration_path %></li>
              <li><%= link_to 'Sign in', new_user_session_path %></li>
            </ul>
          <% end %>
        </nav>
      </div>
    </div>
  </header>

  <div id="main_content" class="container">
    <% flash.each do |name, msg| %>
      <% content_tag(:div, msg, class: "alert alert-#{name}") %>
    <% end %>

    <div id="content" class="col-md-9 center-black">
      <%= yield %>
    </div>

</body>
</html>
```

修改文件 app/assets/styesheets/application.css.scss

```scss
#logo {
  font-size: 26px;
  font-weight: 700;
  text-transform:uppercase;
  letter-spacing: -1px;
  padding: 15 px 0;
  a {
    color: #2F363E;
  }
}

#main_content {
  #content {
    float: none;
  }
  padding-bottom: 100px;
  .link {
    padding: 2em 1em;
    border-bottom: 1px solid #e9e9e9;
    .title {

      a {
        color: #FF4500;
      }
    }
  }
  .comments_title {
    margin-top: 2em;
  }
  #comments {
    .comments {
      padding: 1em 0;
      border-top: 1px solid #E9E9E9;
      .lead {
        margin-bottom: 0;
      }
    }
  }
}
```

修改文件 app/views/links/index.html.erb

```erb
<% @links.each do |link| %>
  <div class="link row clearfix">
    <h2>
      <%= link_to link.title, link %><br>
      <small class="author">Submitted <%=time_ago_in_words(link.created_at) %> by <%= link.user.email %></small>
    </h2>
  </div>
<% end %>
```

修改文件 app/views/links/show.html.erb

```erb
<div class="page-header">
  <h1><a href="<%= @link.url %>"><%= @link.title %></a><br> <small>Submitted by <%= @link.user.email %></small></h1>
</div>

<div class="btn-group">
  <%= link_to 'Visit URL', @link.url, class: "btn btn-primary" %>
</div>

<% if @link.user == current_user -%>
  <div class="btn-group">
  	<%= link_to 'Edit', edit_link_path(@link), class: "btn btn-default" %>
  	<%= link_to 'Destroy', @link, method: :delete, data: {confirm: 'Are you sure?' }, class: "btn btn-default" %>
  </div>
<% end %>
```

修改文件 app/views/links/form.html.erb

```erb
...
<%= form_for(@link) do |f| %>
    <div class="form-group">
      <%= f.label :title %><br>
      <%= f.text_field :title, class: "form-control" %>
    </div>
    <div class="form-group">
      <%= f.label :url %><br>
      <%= f.text_field :url, class: "form-control" %>
    </div>
    <div class="form-group">
      <%= f.submit "Submit", class: "btn btn-lg btn-primary" %>
    </div>
  <% end %>
```

修改文件 app/views/devise/registrations/edit.html.erb

```erb
<h2>Edit <%= resource_name.to_s.humanize %></h2>

<%= form_for(resource, as: resource_name, url: registration_path(resource_name), html: { method: :put }) do |f| %>
  <%= devise_error_messages! %>

  <div class="panel panel-default">

    <div class="panel-body">
      <div class="form-inputs">

        <div class="form-group">
          <%= f.label :email %><br />
          <%= f.email_field :email, class: "form-control", autofocus: true %>
        </div>

        <% if devise_mapping.confirmable? && resource.pending_reconfirmation? %>
          <div>Currently waiting confirmation for: <%= resource.unconfirmed_email %></div>
        <% end %>

        <div class="form-group">
          <%= f.label :password %> <i>(leave blank if you don't want to change it)</i><br />
          <%= f.password_field :password, class: "form-control", autocomplete: "off" %>
        </div>

        <div class="form-group">
          <%= f.label :password_confirmation %><br />
          <%= f.password_field :password_confirmation, autocomplete: "off" %>
        </div>

        <div class="form-group">
          <%= f.label :current_password %> <i>(we need your current password to confirm your changes)</i><br />
          <%= f.password_field :current_password, class: "form-control", autocomplete: "off" %>
        </div>

        <div class="form-group">
          <%= f.submit "Update", class: "btn btn-primary" %>
        </div>

    <% end %>

  </div>

  <div class="panel-footer">

    <h3>Cancel my account</h3>

    <p>Unhappy? <%= button_to "Cancel my account", registration_path(resource_name), data: { confirm: "Are you sure?" }, method: :delete, class: "btn btn-default" %></p>
  </div>
```

修改 app/views/devise/registrations/new.html.erb

```erb
<h2>Sign up</h2>

<%= form_for(resource, as: resource_name, url: registration_path(resource_name)) do |f| %>
  <%= devise_error_messages! %>

  <div class="form-group">
    <%= f.label :email %><br />
    <%= f.email_field :email, autofocus: true, class: "form-control", required: true %>
  </div>

  <div class="form-group">
    <%= f.label :password %>
    <% if @minimum_password_length %>
    <em>(<%= @minimum_password_length %> characters minimum)</em>
    <% end %><br />
    <%= f.password_field :password, autocomplete: "off", class: "form-control", required: true %>
  </div>

  <div class="form-group">
    <%= f.label :password_confirmation %><br />
    <%= f.password_field :password_confirmation, autocomplete: "off", class: "form-control", required: true %>
  </div>

  <div class="form-group">
    <%= f.submit "Sign up", class: "btn btn-lg btn-primary" %>
  </div>
<% end %>

<%= render "devise/shared/links" %>
```

修改文件 app/views/devise/registrations/new

```erb
<h2>Sign up</h2>

<%= form_for(resource, as: resource_name, url: registration_path(resource_name)) do |f| %>
  <%= devise_error_messages! %>

  <div class="form-group">
    <%= f.label :email %><br />
    <%= f.email_field :email, autofocus: true, class: "form-control", required: true %>
  </div>

  <div class="form-group">
    <%= f.label :password %>
    <% if @minimum_password_length %>
    <em>(<%= @minimum_password_length %> characters minimum)</em>
    <% end %><br />
    <%= f.password_field :password, autocomplete: "off", class: "form-control", required: true %>
  </div>

  <div class="form-group">
    <%= f.label :password_confirmation %><br />
    <%= f.password_field :password_confirmation, autocomplete: "off", class: "form-control", required: true %>
  </div>

  <div class="form-group">
    <%= f.submit "Sign up", class: "btn btn-lg btn-primary" %>
  </div>
<% end %>

<%= render "devise/shared/links" %>
```

修改文件 app/views/devise/sessions/new.html.erb

```erb
<h2>Log in</h2>

<%= form_for(resource, as: resource_name, url: session_path(resource_name)) do |f| %>

  <div class="form-group">
    <%= f.label :email %><br />
    <%= f.email_field :email, autofocus: true, class: "form-control", required: false %>
  </div>

  <div class="form-group">
    <%= f.label :password %><br />
    <%= f.password_field :password, autocomplete: "off", class: "form-control", required: false %>
  </div>

  <% if devise_mapping.rememberable? -%>
    <div class="form-group">
      <%= f.check_box :remember_me %>
      <%= f.label :remember_me %>
    </div>
  <% end %>

  <div class="form-group">
    <%= f.submit "Log in", class: "btn btn-primary" %>
  </div>
<% end %>

<%= render "devise/shared/links" %>
```

`git status`
`git add .`
`git commit -am "Add structure and basic styling"`

`git checkout master`
`git merge add_bootstrap`


<h2>添加基本表决权：Add acts as votable<h2>

`git checkout -b add_acts_as_votable`

google搜索: acts as votable

修改文件：Gemfile

```
+	gem 'acts_as_votable'
```

`bundle install`

`rails s`

`rails g acts_as_votable:migration`

修改文件 db/migrate/XXX_acts_as_votable_migration.rb

```rb
-	class ActsAsVotableMigration < ActiveRecord::Migration
+	class ActsAsVotableMigration < ActiveRecord::Migration[5.1]
```

`rake db:migrate`

修改文件 app/model/link.rb

```rb
+	acts_as_votable
	belongs_to :user
```

`rails c`
`@link = Link.first`
`@user = User.first`
`@link.like_by @user`
`@link.votes_for.size`
`@link.save`

修改文件 config/routes.rb

```rb
-	resources :links
+	resources :links do
	+	member do
		+	put "like", to: "links#upvote"
		+	put "dislike", to: "links#downvote"
	+	end
+	end
```

修改文件 controller/links/controller.rb

```rb
+	def upvote
+		@link = Link.find(params[:id])
+		@link.upvote_by current_user
+		redirect_to links_path
+	end

+	def downvote
+		@link = Link.find(params[:id])
+		@link.downvote_by current_user
+		redirect_to links_path
+	end

	private
...
```

修改文件 app/views/links/index.html.erb

```erb
...略
</h2>

+	<div class="btn-group">
+	      <a class="btn btn-default btn-sm" href="<%= link.url %>">Visit Link</a>
+	        <%= link_to like_link_path(link), method: :put, class: "btn btn-default btn-sm" do %>
+	          <span class="glyphicon glyphicon-chevron-up"></span>
	          Upvote
+	          <%= link.get_upvotes.size %>
+	        <% end %>
+	        <%= link_to dislike_link_path(link), method: :put, class: "btn btn-default btn-sm" do %>
+	          <span class="glyphicon glyphicon-chevron-down"></span>
+	          Downvote
+	          <%= link.get_downvotes.size %>
+	        <% end %>
+	    </div>
```

修改文件 app/views/links/show.html.erb

```erb
...略
	end

+	<div class="btn-group pull-right">
+	  <%= link_to like_link_path(@link), method: :put, class: "btn btn-default btn-sm" do %>
+	    <span class="glyphicon glyphicon-chevron-up"></span>
+	    Upvote
+	    <%= @link.get_upvotes.size %>
+	  <% end %>
+	  <%= link_to dislike_link_path(@link), method: :put, class: "btn btn-default btn-sm" do %>
+	    <span class="glyphicon glyphicon-chevron-down"></span>
+	    Upvote
+	    <%= @link.get_downvotes.size %>
+	  <% end %>
+	</div>
```

`git status`
`git add .`
`git commit -am "Add acts as votable"`

`git checkout master`
`git merge add_acts_as_votable`


<h2>添加留言板：Add comments</h2>

`git checkout -b add_comments`

`rails g scaffold Comment link_id:integer:index body:text user:references --skip-stylesheets`

`rake db:migrate`

修改文件 Gemfile

```
+	gem 'simple_form'
```

`bundle install`
重启`rails s`


修改文件 app/models/link.rb

```rb
+	has_many :comments
```

修改文件 app/models/comment.rb

```rb
+	belongs_to :link
```

修改文件 config/routes.rb

```rb
	resources :links do
		member do
		......
		end
	+	resources :comments
	end
```

`rake routes`

修改文件 CommentsController.rb

```rb
-	def index
-	  @comments = Comment.all
-	end

-	def show
-	end

-	def new
-	  @comment = Comment.new
-	end

-	def edit
-	end

	def create
+	  @link = Link.find(params[:link_id])
	  @comment = @link.comments.new(comment_params)
+	  @comment.user = current_user

	  respond_to do |format|
	    if @comment.save
	      format.html { redirect_to @link, notice: 'Comment was successfully created.' }
	      format.json { render json: @comment, status: :created, location: @comment }
	    else
	      format.html { render action: "new" }
	      format.json { render json: @comment.errors, status: :unprocessable_entity }
	    end
	  end
	end

...

-	def update
-	  ...
-	end
```

修改文件 app/views/links/show.html.erb

```erb
... #加在最下面
	<h3 class="comments_title">
	  <%= @link.comments.count %> Comments
	</h3>

	<div id="comments">
	  <%= render :partial => @link.comments %>
	</div>
	<%= simple_form_for [@link, Comment.new]  do |f| %>
	  <div class="field">
	    <%= f.text_area :body, class: "form-control" %>
	  </div>
	  <br>
	  <%= f.submit "Add Comment", class: "btn btn-primary" %>
	<% end %>
```

增加文件

`touch app/views/comments/_comment.html.erb`

修改文件 Gemfile

```
+	gem 'record_tag_helper', '~> 1.0'
```

`bundle install`
重启`rails s`



修改文件 app/views/comments/comment.html.erb

```erb
<%= div_for(comment) do %>
  <div class="comments_wrapper clearfix">
  	<div class="pull-left">
  	  <p class="lead"><%= comment.body %></p>
  	  <p><small>Submitted <strong><%= time_ago_in_words(comment.created_at) %> ago</strong> by <%= comment.user.email %></small></p>
  	</div>

  	<div class="btn-group pull-right">
  	  <% if comment.user == current_user -%>
  	    <%= link_to 'Destroy', comment, method: :delete, date: { confirm: 'Are you sure?' }, class: "btn btn-sm btn-default" %>
  	  <% end %>
  	</div>
  </div>
<% end %>
```

`git status`
`git add .`
`git comment -am "Add comments"`

`git checkout master`
`git merge Add_comments`


<h2>添加名字到用户：Add name to users</h2>

`git checkout -b Add_name_to_users`

`rials g migration add_name_to_users name:string`

`rake db:migrate`

修改文件 app/views/devise/registrations/edit.html.erb

```erb
+	<div class="form-group">
+     <%= f.label :name %><br />
+     <%= f.text_field :name, autofocus: true, class: "form-control", required: true %>
+   </div>
```

修改文件 app/controller/application_controller.rb

```rb
+	before_action :configure_permitted_parameters, if: :devise_controller?

+	protected

+	def configure_permitted_parameters
+     devise_parameter_sanitizer.permit(:sign_up) do |u|
+       u.permit(:name, :email, :password, :password_confirmation)
+	  end
+     devise_parameter_sanitizer.permit(:account_update) do |u|
+       u.permit(:name, :email, :password, :password_confirmation, :current_password)
+     end
+	end
```

修改文件 app/views/links/index.html.erb

```erb
-	      <small class="author">Submitted <%=time_ago_in_words(link.created_at) %> by <%= link.user.email %></small>
+	      <small class="author">Submitted <%=time_ago_in_words(link.created_at) %> by <%= link.user.name %></small>
```

修改文件 app/views/links/show.html.erb

```erb
-	  <h1><a href="<%= @link.url %>"><%= @link.title %></a><br> <small>Submitted by <%= @link.user.email %></small></h1>
+	  <h1><a href="<%= @link.url %>"><%= @link.title %></a><br> <small>Submitted by <%= @link.user.name %></small></h1>
```

修改文件 app/views/comments/_comments.html.erb

```erb
-	<p><small>Submitted <strong><%= time_ago_in_words(comment.created_at) %> ago</strong> by <%= comment.user.email %></small></p>
+	<p><small>Submitted <strong><%= time_ago_in_words(comment.created_at) %> ago</strong> by <%= comment.user.name %></small></p>
```

修改文件 app/views/devise/registration/new.html.erb

```erb
+	<div class="form-group">
+     <%= f.label :name %><br />
+     <%= f.text_field :name, autofocus: true, class: "form-control", required: true %>
+   </div>

	<div class="form-group">
	  <%= f.label :email %><br />
	....
```

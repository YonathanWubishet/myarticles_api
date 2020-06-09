* Ruby version: 
ruby 2.6, rails 6.0.3

* Database: 
MySQL 8.0

Steps for building this api:

1. Run 'rails new myarticles -d mysql --api' (this will create the api scaffold with the mysql2 gem added in the Gem file and all other mysql dependecies.)
2. Change the database names to the desired names inside database.yml file.(You can also assign a mysql password.)
3. install Msql server (choose the legacy password, for ease) and remember the password for the server and update the root user in the  databse.yml file with that password.
4. Run 'rails db:create', to create the databse, (alternatively, use phpmyadmin to create the databse).
5. run 'rails s' and you should get 'Yay, You're on rails.'
6. run 'rails g model Article title:string body:text'(You may add other features such as slug;string, to slugify web addresses.)
7. Add the following validation in the Article model. Should look like: class Article < ApplicationRecord
    validates :title, presence: true
    validates :body, presence: true
end
8. run'rails db:migrate' to populate created databse tables.
9. Add 'gem 'faker'' to the Gemfile and run 'bundle install'.
10. And add the following to the seeds.rb file, to have some data, to work with. Should look like:
5.times do
    Article.create({
        title: Faker::Book.title,
        body: Faker::Lorem.sentence
    })
end
11. Run 'rails db:seed'
12. Create a folder called api and a sub folder called v1 to enable updating the api separately, incase that is needed down the road.
9. Create a controller manually in VScode, within that subfolder. Should look like:
module Api
    module V1
      class ArticlesController < ApplicationController
        def index
          articles = Article.order('created_at DESC');
          render json: {status: 'SUCCESS', message:'Loaded articles', data:articles},status: :ok
        end
  
        def show
          article = Article.find(params[:id])
          render json: {status: 'SUCCESS', message:'Loaded article', data:article},status: :ok
        end
  
        def create
          article = Article.new(article_params)
  
          if article.save
            render json: {status: 'SUCCESS', message:'Saved article', data:article},status: :ok
          else
            render json: {status: 'ERROR', message:'Article not saved', data:article.errors},status: :unprocessable_entity
          end
        end
  
        def destroy
          article = Article.find(params[:id])
          article.destroy
          render json: {status: 'SUCCESS', message:'Deleted article', data:article},status: :ok
        end
  
        def update
          article = Article.find(params[:id])
          if article.update_attributes(article_params)
            render json: {status: 'SUCCESS', message:'Updated article', data:article},status: :ok
          else
            render json: {status: 'ERROR', message:'Article not updated', data:article.errors},status: :unprocessable_entity
          end
        end
  
        private
  
        def article_params
          params.permit(:title, :body)
        end
      end
    end
  end
10. Create a routes. Should look like:
Rails.application.routes.draw do
  namespace 'api' do
    namespace 'v1' do
      resources :articles
    end
  end
end
11. run'rails routes' to check the routes.
12. Test the Get, Put, Delete and Post/Patch Http requests either on Postman or Insomnia.
For Get: type http://localhost:3000/api/v1/articles onto the Get field, and click send. It should retrieve the data from the API.
For Post: type http://localhost:3000/api/v1/articles onto the Post field, and click on the params key field and type 'Content type"
and on the value field, application/jason. Then click on Body tab, and click on 'raw' radio button, and in the field below, insert Json:
Example:
{
    "title":"Test Article One",
    "body":"This is is the body"
}
Then click send. It should post/send the new data to the API.
For Delete: type http://localhost:3000/api/v1/articles/6 onto the Delete field, and click send. It should remove the data specified by the id from the API(in this case, article 6).
For Put/Patch: type http://localhost:3000/api/v1/articles/5 onto the Post field, and click on the params key field and type 'Content type"
and on the value field, application/jason. Then click on Body tab, and click on 'raw' radio button, and in the field below, insert Json:
Example:
{
    "title":"Updated Title",
    "body":"We've updated the content."
}
Then click send. It should update the specified existing data(In this case, article 5).
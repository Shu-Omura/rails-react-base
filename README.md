# rails-react-base

## Version
- Rails 6.0.3.4
- Ruby 2.7.2
- React 17.0.1
- Docker 2.4.0.0

## Setup

```sh
docker-compose build \
&& docker-compose run --rm webpack sh -c 'cd front && yarn install' \
&& docker-compose run --rm web bash -c "cd api && bundle config set path 'vendor/bundle' && bundle install" \
&& docker-compose run --rm web bash -c "cd api && bundle exec rails db:create"

docker-compose up -d web
```

If you want to use rspec for test framework, you should exec following command.
```sh
docker-compose run --rm web bash -c "cd api && bundle exec rails g rspec:install"
```
If you cannot work `bundle exec rails db:create`, you should add `bundle install &&`.

## Reset

```sh
docker-compose down -v \
&& docker-compose build \
&& docker-compose run --rm webpack sh -c 'cd front && yarn install' \
&& docker-compose run --rm web "cd api && bundle config set path 'vendor/bundle' && bundle install" \
&& docker-compose run --rm web "cd api && bundle exec rails db:create && bundle exec rails db:migrate"

docker-compose up -d web
```

##
### If you want to set up by yourself, please take next step.
1. Make directories following

```sh
.
│─ api
│   ├─ Gemfile
│   └─ Gemfile.lock
│
│─ front
│
│─ docker
│   ├ api
│   │  └─ Dockerfile
│   └─ front
│       └─ Dockerfile
│
└─ docker-compose.yml
```

- Gemfile
```ruby
source 'https://rubygems.org'
gem 'rails', '~>6'
```
- `Gemfile.lock` is empty.
- `Dockerfile` and `docker-compose.yml`are save in this repository.

2. Comment out volumes `node_modules:/app/front/node_modules` in docker-compose.yml

3. Build and create react and rails application.
```sh
docker-compose build \
&& docker-compose run --rm webpack sh -c "npx create-react-app front --typescript" \
&& docker-compose run --rm web rails new api --force --database=mysql --skip-bundle --skip-test --api
```

4. Revert comment out volumes `node_modules:/app/front/node_modules` in docker-compose.yml

5. Edit `Gemfile`, `database.yml`, `config/application.rb`
- `Gemfile` is customaized for your project. -> if you are ok, can use this repository's.
- `database.yml` is modified for mysql. -> same in this repository.
- `config/application.rb` is modified for using React in frontend. -> same in this repository.

6. Bundle install
```sh
docker-compose run --rm web bash -c "cd api && bundle config set path 'vendor/bundle' && bundle install"
```

7. Create database
```sh
docker-compose run --rm web bash -c "cd api && bundle exec rails db:create"
# docker-compose run --rm web bash -c "cd api && bundle install && bundle exec rails db:create"
```

8. Finish!
```sh
docker-compose up -d web
```

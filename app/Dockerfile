FROM ruby:2.5

RUN apt-get update && apt-get install -y nano curl unzip wget

WORKDIR /app

RUN touch /etc/app-env

ADD  https://github.com/TracksApp/tracks/archive/v2.4.1.zip /app

RUN  unzip v2.4.1.zip && mv /app/tracks-2.4.1/* /app && rm /app/v2.4.1.zip

RUN gem install bundler && bundle install

RUN mkdir -p /app/log

ENV RAILS_ENV="production"

ENV RAILS_SERVE_STATIC_FILES="true"

COPY puma.rb /app/config/puma.rb
COPY site.yml /app/config/site.yml
COPY database-sqlite.yml /app/config/database.yml

RUN bundle exec rake db:migrate RAILS_ENV=production && bundle exec rake assets:precompile

RUN openssl req -x509 -nodes -days 3650 -newkey rsa:2048 \
    -keyout tracks.key -out tracks.crt \
    -subj "/C=RU/ST=Moscow/L=Moscow/O=TRACKS/OU=DS/CN=TRACKS/emailAddress=..."

EXPOSE 3000

CMD "rails" "s" "-b" "ssl://0.0.0.0:3000?key=/app/tracks.key&cert=/app/tracks.crt"

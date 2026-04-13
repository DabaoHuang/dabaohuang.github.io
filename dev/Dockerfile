FROM ruby:3.2-alpine

RUN apk add --no-cache build-base git

WORKDIR /site

COPY Gemfile /tmp/blog-dev/Gemfile
ENV BUNDLE_GEMFILE=/tmp/blog-dev/Gemfile
RUN bundle config set path /usr/local/bundle \
  && bundle install

EXPOSE 4000

CMD ["bundle", "exec", "jekyll", "serve", "--host", "0.0.0.0", "--port", "4000", "--watch", "--force_polling", "--source", "/site", "--config", "/site/_config.yml,/site/dev/_config.dev.yml"]

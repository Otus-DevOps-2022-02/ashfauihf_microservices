FROM ruby:2.2
USER root
RUN apt-get update -y && \
    apt-get --assume-yes install apt-transport-https ca-certificates && \
    apt-get install -y build-essential


ENV APP_HOME /app
RUN mkdir $APP_HOME
WORKDIR $APP_HOME

ADD Gemfile* $APP_HOME/
RUN bundle install
ADD . $APP_HOME

ENV COMMENT_DATABASE_HOST comment_db
ENV COMMENT_DATABASE comments

CMD ["puma"]

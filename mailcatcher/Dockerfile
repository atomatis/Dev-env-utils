FROM ruby
RUN gem install mailcatcher
EXPOSE 25 1080
CMD ["mailcatcher", "-f", "--ip=0.0.0.0", "--smtp-port=25"]

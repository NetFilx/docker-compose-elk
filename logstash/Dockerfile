FROM hub.c.163.com/library/logstash:5.2.1

RUN mkdir /home/logs/
COPY ./logs /home/logs/
RUN chmod -R 777 /home/logs/

# Add your logstash plugins setup here
# Example: RUN logstash-plugin install logstash-filter-json

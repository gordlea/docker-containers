FROM grafana/grafana-oss:9.0.6 as builder
USER root
RUN apk update && apk add -u nodejs npm bash git yarn go
ENV PATH="/root/go/bin:${PATH}"
RUN cd /root && git clone https://github.com/magefile/mage && \
	cd mage && \
	go run bootstrap.go

RUN cd /var/lib/grafana/plugins/ && git clone https://github.com/grafana/mqtt-datasource.git && \
	cd mqtt-datasource && \
	yarn && \
	yarn build
RUN	rm -rf /var/lib/grafana/plugins/mqtt-datasource/node_modules


FROM grafana/grafana-oss:9.0.6
COPY --from=builder /var/lib/grafana/plugins /var/lib/grafana_plugins/
ENV GF_PLUGINS_ALLOW_LOADING_UNSIGNED_PLUGINS=grafana-mqtt-datasource \
	GF_PATHS_PLUGINS=/var/lib/grafana_plugins/

USER root
RUN chown -R grafana /var/lib/grafana_plugins
USER grafana

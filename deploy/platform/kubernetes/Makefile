# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations
# under the License.
#

include ../../../Makefile.in
include Makefile.in

HELM_OPTIONS := $(HELM_OPTIONS) --set skywalking.fullnameOverride=$(RELEASE)
HELM_OPTIONS := $(HELM_OPTIONS) --set skywalking.oap.image.repository=$(SW_OAP_IMAGE) --set skywalking.oap.image.tag=$(SW_OAP_IMAGE_TAG)
HELM_OPTIONS := $(HELM_OPTIONS) --set skywalking.ui.image.repository=$(SW_UI_IMAGE) --set skywalking.ui.image.tag=$(SW_UI_IMAGE_TAG)
HELM_OPTIONS := $(HELM_OPTIONS) --set skywalking.ui.env.SW_ZIPKIN_ADDRESS=http://$(RELEASE)-oap.$(NAMESPACE).svc:9412
HELM_OPTIONS := $(HELM_OPTIONS) --set skywalking.satellite.image.repository=$(SW_SATELLITE_IMAGE) --set skywalking.satellite.image.tag=$(SW_SATELLITE_IMAGE_TAG)
HELM_OPTIONS := $(HELM_OPTIONS) --set swck.image.repository=$(SWCK_OPERATOR_IMAGE) --set swck.image.tag=$(SWCK_OPERATOR_IMAGE_TAG)
HELM_OPTIONS := $(HELM_OPTIONS) --set sampleServices.hub=$(HUB) --set sampleServices.tag=$(TAG) --set sampleServices.namespace=$(SAMPLE_SERVICES_NAMESPACE)
HELM_OPTIONS := $(HELM_OPTIONS) --set opentelemetry-collector.image.repository=$(OTEL_COLLECTOR_IMAGE) --set opentelemetry-collector.image.tag=$(OTEL_COLLECTOR_IMAGE_TAG)
HELM_OPTIONS := $(HELM_OPTIONS) --set features.rover.swctl.image=$(SW_CLI_IMAGE)
HELM_OPTIONS := $(HELM_OPTIONS) --set features.traceProfiling.swctl.image=$(SW_CLI_IMAGE)
HELM_OPTIONS := $(HELM_OPTIONS) --set features.event.exporter.image=$(SW_EVENT_EXPORTER_IMAGE)
HELM_OPTIONS := $(HELM_OPTIONS) --set features.javaAgentInjector.agentImage=$(SW_AGENT_JAVA_IMAGE)
HELM_OPTIONS := $(HELM_OPTIONS) --set features.rover.image=$(SW_ROVER_IMAGE)
HELM_OPTIONS := $(HELM_OPTIONS) --set features.promql.grafana.image=$(GRAFANA_IMAGE)


features := $(subst $(comma), ,$(FEATURE_FLAGS))
features := $(foreach f,$(features),feature-$(f))

ifeq ($(DEBUG),true)
	HELM_OPTIONS := $(HELM_OPTIONS) --dry-run
endif

BACKEND_SERVICE := $(RELEASE)-oap.$(NAMESPACE).svc:11800
ifneq (,$(findstring satellite,$(features)))
	BACKEND_SERVICE := $(RELEASE)-satellite.$(NAMESPACE).svc:11800
endif

.PHONY: helm
helm:
	@helm version >/dev/null 2>&1 || (echo "No helm in PATH, please make sure helm is available in PATH."; exit 1)

# @feature: java-agent-injector; use the java agent injector to inject the java agent more natively
.PHONY: feature-java-agent-injector
feature-java-agent-injector:
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set features.javaAgentInjector.enabled=true)

.PHONY: feature-single-node
feature-single-node:
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set skywalking.oap.replicas=1)

.PHONY: feature-cluster
feature-cluster:
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set skywalking.oap.replicas=2)

.PHONY: feature-elasticsearch
feature-elasticsearch:
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set skywalking.oap.storageType=elasticsearch)
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set skywalking.elasticsearch.enabled=true)

.PHONY: feature-postgresql
feature-postgresql:
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set skywalking.oap.storageType=postgresql)
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set skywalking.postgresql.enabled=true)

.PHONY: feature-so11y
feature-so11y:
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set features.so11y.enabled=true)
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set opentelemetry.enabled=true)

.PHONY: feature-vm-monitor
feature-vm-monitor:
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set features.vmMonitor.enabled=true)
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set opentelemetry.enabled=true)

.PHONY: feature-event
feature-event:
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set features.event.enabled=true)

.PHONY: feature-istiod-monitor
feature-istiod-monitor:
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set features.istiodMonitor.enabled=true)
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set istiod.global.istioNamespace=istio-system --set base.global.istioNamespace=istio-system)
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set opentelemetry.enabled=true)

.PHONY: feature-als
feature-als:
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set features.als.enabled=true)
#$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set istio.enabled=true)
#$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set istiod.meshConfig.defaultConfig.tracing.zipkin.address=$(RELEASE)-oap.$(NAMESPACE).svc:9411)
#$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set istiod.meshConfig.defaultConfig.envoyMetricsService.address=$(BACKEND_SERVICE))
#$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set istiod.meshConfig.defaultConfig.envoyAccessLogService.address=$(BACKEND_SERVICE))

.PHONY: feature-satellite
feature-satellite:
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set features.satellite.enabled=true)

.PHONY: feature-rover
feature-rover:
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set features.rover.enabled=true)

.PHONY: feature-trace-profiling
feature-trace-profiling:
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set features.traceProfiling.enabled=true)

.PHONY: feature-mysql-monitor
feature-mysql-monitor:
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set features.mysqlMonitor.enabled=true)
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set opentelemetry.enabled=true)

.PHONY: feature-postgresql-monitor
feature-postgresql-monitor:
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set features.postgresqlMonitor.enabled=true)
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set opentelemetry.enabled=true)

.PHONY: feature-apisix-monitor
feature-apisix-monitor:
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set features.apisixMonitor.enabled=true)
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set opentelemetry.enabled=true)

# @feature: kubernetes-monitor; extra resources to install for kubernetes monitoring, standard kube-state-metrics
.PHONY: feature-kubernetes-monitor
feature-kubernetes-monitor:
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set features.kubernetesMonitor.enabled=true)
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set kube-state-metrics.enabled=true)
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set opentelemetry.enabled=true)

.PHONY: feature-function
feature-function:
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set features.function.enabled=true)
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set openfunction.enabled=true)

.PHONY: feature-promql
feature-promql:
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set features.promql.enabled=true)
	@mkdir -p ./generated/promql-configs/
	@cp ../config/promql/dashboards/*.json ./generated/promql-configs/

.PHONY: feature-mesh-with-agent
feature-mesh-with-agent:
	$(eval HELM_OPTIONS := $(HELM_OPTIONS) --set features.meshWithAgent.enabled=true)

.PHONY: deploy
deploy: $(features)
	helm dep up .
	helm -n $(NAMESPACE) upgrade --install $(RELEASE) . --create-namespace --timeout=20m $(HELM_OPTIONS)

	$(info rollout restart to allow the new sidecar to be injected.)
	kubectl -n $(SAMPLE_SERVICES_NAMESPACE) get deployments -o name | xargs kubectl -n $(SAMPLE_SERVICES_NAMESPACE) rollout restart

.PHONY: undeploy
undeploy:
	helm -n $(NAMESPACE) delete $(RELEASE)

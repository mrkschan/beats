#!/bin/bash

BEATNAME=metricbeat
SYSTEM_TESTS=true
TEST_ENVIRONMENT?=true
GOPACKAGES=$(shell go list ${BEAT_DIR}/${BEATNAME}/... | grep -v /vendor/)
ES_BEATS?=..

# Metricbeat can only be cross-compiled on platforms not requiring CGO which
# are the same platforms where the system metrics (cpu, memory) are not
# implemented.
GOX_OS=solaris freebsd netbsd
GOX_FLAGS='-arch=amd64'

include ${ES_BEATS}/libbeat/scripts/Makefile

# Collects all module dashboards
.PHONY: kibana
kibana:
	-rm -r etc/kibana
	mkdir -p etc/kibana
	-cp -r module/*/_beat/kibana/ etc/kibana

# Collects all module and metricset fields
.PHONY: fields
fields:
	cat ${ES_BEATS}/metricbeat/etc/_beat/fields_base.yml > etc/fields.yml
	python ${ES_BEATS}/metricbeat/scripts/fields_collector.py >> etc/fields.yml

# Collects all module docs
.PHONY: docs
docs:
	-rm -r docs/modules
	mkdir -p docs/modules
	python ${ES_BEATS}/metricbeat/scripts/docs_collector.py

# Collects all module configs
.PHONY: configs
configs:
	python ${ES_BEATS}/metricbeat/scripts/config_collector.py > etc/beat.yml

# This is called by the beats packer before building starts
.PHONY: before-build
before-build:

# Runs all collection steps and updates afterwards
.PHONY: collect
collect: kibana fields docs configs update

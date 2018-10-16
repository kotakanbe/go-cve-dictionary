.PHONY: \
	dep \
	depup \
	build \
	install \
	all \
	vendor \
	lint \
	vet \
	fmt \
	fmtcheck \
	pretest \
	test \
	integration \
	cov \
	clean

SRCS = $(shell git ls-files '*.go')
PKGS =  ./commands ./config ./db ./log ./models ./fetcher ./fetcher/jvn/xml ./fetcher/nvd ./fetcher/nvd/xml ./fetcher/nvd/json ./server ./util
VERSION := $(shell git describe --tags --abbrev=0)
REVISION := $(shell git rev-parse --short HEAD)
LDFLAGS := -X 'main.version=$(VERSION)' \
	-X 'main.revision=$(REVISION)'

all: dep build test

dep:
	go get -u github.com/golang/dep/...
	dep ensure -v

depup:
	go get -u github.com/golang/dep/...
	dep ensure -update -v

build: main.go dep
	go build -ldflags "$(LDFLAGS)" -o go-cve-dictionary $<

install: main.go dep
	go install -ldflags "$(LDFLAGS)"

all: test

lint:
	@ go get -v golang.org/x/lint/golint
	$(foreach file,$(SRCS),golint $(file) || exit;)

vet:
	$(foreach pkg,$(PKGS),go vet $(pkg);)

fmt:
	gofmt -w $(SRCS)

fmtcheck:
	$(foreach file,$(SRCS),gofmt -d $(file);)

pretest: lint vet fmtcheck

test: pretest
	$(foreach pkg,$(PKGS),go test -v $(pkg) || exit;)

integration:
	go test -tags docker_integration -run TestIntegration -v

cov:
	@ go get -v github.com/axw/gocov/gocov
	@ go get golang.org/x/tools/cmd/cover
	gocov test | gocov report

clean:
	$(foreach pkg,$(PKGS),go clean $(pkg) || exit;)


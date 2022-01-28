# Versioned by a simple counter that is not bound to a specific CosmWasm version
# See builders/README.md
BUILDERS_PREFIX := mandrean/libwasmvm-builder:0009

# https://docs.docker.com/buildx/working-with-buildx/
PLATFORMS = ""
OUTPUT = "type=docker"
UNAME_M := $(shell uname -m)
ifeq ($(UNAME_M),arm64)
	PLATFORMS = "linux/arm64"
endif
ifeq ($(UNAME_M),x86_64)
	PLATFORMS = "linux/amd64"
endif

.PHONY: docker-buildx
docker-buildx:
	docker buildx inspect --builder builder >>/dev/null 2>&1 || (docker buildx create --name builder)
	docker buildx use builder >>/dev/null 2>&1
	docker buildx inspect --bootstrap >>/dev/null 2>&1

.PHONY: docker-image-debian
docker-image-debian:
	docker buildx build . \
		--pull \
		-t $(BUILDERS_PREFIX)-debian \
		-f ./Dockerfile.debian \
		--platform $(PLATFORMS) \
		--output $(OUTPUT)

.PHONY: docker-image-cross
docker-image-cross:
	docker buildx build . \
		-t $(BUILDERS_PREFIX)-cross \
		-f ./Dockerfile.cross \
		--platform $(PLATFORMS) \
		--output $(OUTPUT)

.PHONY: docker-image-alpine
docker-image-alpine:
	docker buildx build . \
		-t $(BUILDERS_PREFIX)-alpine \
		-f ./Dockerfile.alpine \
		--platform $(PLATFORMS) \
		--output $(OUTPUT)

.PHONY: docker-images
docker-images: docker-image-debian docker-image-cross docker-image-alpine

.PHONY: docker-publish
docker-publish: OUTPUT="type=registry"
docker-publish: docker-images

.PHONY: docker-publish-multi
docker-publish-multi: PLATFORMS="linux/arm64,linux/amd64"
docker-publish-multi: OUTPUT="type=registry"
docker-publish-multi: docker-images

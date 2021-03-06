.PHONY: tag push

BASE=alpine:3.5
IMAGE=ca-certificates

SHA_IMAGE=alpine:3.5@sha256:dfbd4a3a8ebca874ebd2474f044a0b33600d4523d03b0df76e5c5986cb02d7e8

default: push

hash: Dockerfile
	DOCKER_CONTENT_TRUST=1 docker pull $(BASE)
	tar cf - $^ | docker build --no-cache -t $(IMAGE):build -
	CONTAINER=$$(docker create $(IMAGE):build /dev/null); \
		mkdir tmp && docker export $${CONTAINER} | tar xf - -C tmp && \
		cat tmp/etc/ssl/certs/ca-certificates.crt | docker run --rm -i $(SHA_IMAGE) sha1sum - | sed 's/ .*//' > $@ && \
		rm -rf tmp && docker rm $${CONTAINER}

push: hash
	docker pull linuxkit/$(IMAGE):$(shell cat hash) || \
		(docker tag $(IMAGE):build linuxkit/$(IMAGE):$(shell cat hash) && \
		 docker push linuxkit/$(IMAGE):$(shell cat hash))
	docker rmi $(IMAGE):build
	rm -f hash

tag: hash
	docker pull linuxkit/$(IMAGE):$(shell cat hash) || \
		docker tag $(IMAGE):build linuxkit/$(IMAGE):$(shell cat hash)
	docker rmi $(IMAGE):build
	rm -f hash

clean:
	rm -f hash tmp

.DELETE_ON_ERROR:

IMAGE := ministryofjustice/cloud-platform-runbooks
VERSION := 1.5 # Change this in .circleci/config.yml if you update it here

.built-docker-image: Dockerfile Gemfile
	docker build -t $(IMAGE) .
	touch .built-docker-image

docker-push: .built-docker-image
	docker tag $(IMAGE) $(IMAGE):$(VERSION)
	docker push $(IMAGE):$(VERSION)

server: .built-docker-image
	mkdir compiled || true
	docker run \
		-p 4567:4567 \
		-v $$(pwd)/source:/app/source \
		-v $$(pwd)/compiled:/app/compiled \
		-v $$(pwd)/config:/app/config \
		-it \
		$(IMAGE):$(VERSION) bundle exec middleman server

# The CircleCI build pipeline does this, so it should never
# be necessary to run this task. I'm leaving it here for
# reference, and in case we ever need to push changes
# manually.
build: .built-docker-image
	mkdir compiled || true
	docker run \
		-v $$(pwd)/source:/app/source \
		-v $$(pwd)/compiled:/app/compiled \
		-v $$(pwd)/config:/app/config \
		-it \
		$(IMAGE) bundle exec middleman build --build-dir compiled
	touch compiled/.nojekyll

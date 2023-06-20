VERSION 0.7 # https://docs.earthly.dev/docs/earthfile#version
FROM python:3

ARG --global build_dir="public"

# Build the site
build:
    FROM klakegg/hugo:alpine
    #FROM klakegg/hugo:ext-alpine # If you need additional tools (nodejs, yarn, go, babel...) https://github.com/klakegg/docker-hugo#hugo-extended-edition

    ENV HUGO_ENV=production

    COPY . .

    #RUN npm ci
    RUN hugo --destination="$build_dir"

    SAVE ARTIFACT $build_dir build_result AS LOCAL $build_dir

##################
# Helper targets #
##################

# Run the `hugo server` dev server from the host. Run locally with `earthly +devserver`.
devserver:
    ARG port=1313
    ARG container_name="earthly-dev-server"

    LOCALLY
        WITH DOCKER
            # https://hub.docker.com/r/klakegg/hugo
            RUN docker run --rm \
                           --volume="$PWD:/src" \
                           --publish $port:1313 \
                           --name="$container_name" \
                           klakegg/hugo:alpine \
                           server
	END


################
# Swift upload #
################

# Base environment for Swift
swift-deps:
    FROM python:3
    RUN pip install python-keystoneclient python-swiftclient


# User-Defined Command to run Swift
SWIFT_UPLOAD:
    COMMAND
    ARG --required file_or_directory
    RUN --secret OS_USERNAME \
        --secret OS_PASSWORD \
        --secret OS_AUTH_URL \
        --secret OS_AUTH_VERSION \
        --secret OS_TENANT_NAME \
        --secret OS_STORAGE_URL \
        --secret OS_CONTAINER_NAME \
        --push \
        -- \
        swift upload --changed --object-name "" $OS_CONTAINER_NAME ./$file_or_directory

SWIFT_EMPTY_BUCKET:
    COMMAND
    RUN --push \
        --secret OS_USERNAME \
        --secret OS_PASSWORD \
        --secret OS_AUTH_URL \
        --secret OS_AUTH_VERSION \
        --secret OS_TENANT_NAME \
        --secret OS_STORAGE_URL \
        --secret OS_CONTAINER_NAME \
        -- \
        swift list $OS_CONTAINER_NAME | xargs --no-run-if-empty --verbose swift delete $OS_CONTAINER_NAME


# Run `swift list` on the container
swift-list:
    FROM +swift-deps

    RUN --push \
        --secret OS_USERNAME \
        --secret OS_PASSWORD \
        --secret OS_AUTH_URL \
        --secret OS_AUTH_VERSION \
        --secret OS_TENANT_NAME \
        --secret OS_STORAGE_URL \
        --secret OS_CONTAINER_NAME \
        -- \
        swift list $OS_CONTAINER_NAME


# Copy the site that was built in `+build`, then upload it via Swift
upload-site:
    FROM +swift-deps
    COPY +build/build_result ./to-upload

    DO +SWIFT_EMPTY_BUCKET
    DO +SWIFT_UPLOAD --file_or_directory=./to-upload


# Upload a robots.txt file
upload-norobots:
    FROM +swift-deps

    RUN echo 'Disallow: /' >> robots.txt
    DO +SWIFT_UPLOAD --file_or_directory=./robots.txt


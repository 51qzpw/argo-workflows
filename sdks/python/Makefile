GIT_TAG               := $(shell git describe --exact-match --tags --abbrev=0 2> /dev/null || echo untagged)
ifeq ($(GIT_TAG),untagged)
VERSION               := 0.0.0-latest
else
# remove the "v" prefix, not allowed
VERSION               := $(subst v,,$(GIT_TAG))
endif

# work dir
WD                    := $(shell echo "`pwd`/client")

DOCKER = docker run --rm -v $(WD):/wd --workdir /wd
CHOWN = chown -R $(shell id -u):$(shell id -g)

publish: generate
	echo "TODO: Publish the package to PyPI"

generate:
	rm -Rf $(WD)
	mkdir -p $(WD)
	cat ../../api/openapi-spec/swagger.json | \
		sed 's/io.k8s.api.core.v1.//' | \
		sed 's/io.k8s.apimachinery.pkg.apis.meta.v1.//' \
		> $(WD)/swagger.json
	$(DOCKER) openapitools/openapi-generator-cli:v5.2.1 \
		generate \
		--input-spec /wd/swagger.json \
		--generator-name python \
		--output /wd \
		--additional-properties packageVersion=${VERSION} \
		--additional-properties packageName="argo_workflows" \
		--additional-properties projectName="argo-workflows" \
		--additional-properties hideGenerationTimestamp=true \
		--remove-operation-id-prefix \
		--model-name-prefix '' \
		--model-name-suffix '' \
		--artifact-id argo-python-client \
		--global-property modelTests=false \
 		--global-property packageName=argo_workflows \
		--generate-alias-as-model
	# https://vsupalov.com/docker-shared-permissions/#set-the-docker-user-when-running-your-container
	$(CHOWN) $(WD) || sudo $(CHOWN) $(WD)
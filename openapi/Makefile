html: openCDE-spec.yaml
	docker run --rm \
			-v ${PWD}:/local \
			-u $$(id -u):$$(id -g) \
    		openapitools/openapi-generator-cli generate \
			-i /local/openCDE-spec.yaml \
    		-g html \
    		-o /local/out/html
	
clean:
	rm -rf out

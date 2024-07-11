```Makefile
src_files=src/*.go
unit-test:	
	go test $(src_files)
build:
	go build $(src_files)
lint:
	go vet $(src_files)
	go install honnef.co/go/tools/cmd/staticcheck@latest
	staticcheck $(src_files)
version:
    go build -o bin/go-sample -ldflags="-X 'main.Version=$(BUILD_VERSION)'" -v $(src_files)
```
Configure files for install, build, unit test, and e2e test tasks.

- Ensure that the `go.mod` and `go.sum` files exist in the root of your repository.
Dependencies are downloaded using `go mod download` and `go mod verify` during the `install` task.

- Ensure that the entrypoint of your application exists in the file `main.go` in the root of your repository.
The application is compiled using `go build main.go` during the `build` task.

- Ensure that your repository contains a `Makefile` in the root of the repository with a command available called `unit-test`.
The command `make unit-test` is used to run unit tests during the `unit-test` task.

- Ensure that your repository contains a file named `e2e.sh`.
The `e2e.sh` script is used to run e2e tests during the `deploy-verification-preprod-environment` and `deploy-verification-production-environment` tasks.

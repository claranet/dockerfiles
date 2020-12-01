# Terraform CI

Docker image for terraform CI like for [SignalFx detectors](https://github.com/claranet/terraform-signalfx-detectors).

## What does it contain?

The image is based on official `terraform` image and add some tools like :

- `terraform-docs`
- `terraform-config-inspect`
- `tflint`
- `bash`
- `curl`
- `grep`
- `jq`
- `coreutils`
- `j2cli`
- `doctoc`
- `make`
- `bash-completion`

## Usage

The docker container uses `bash` as entry-point which allows the user to run any tool included in the image.

For example, run `tflint`:

```bash
docker run -v "$PWD:/work" claranet/terraform-ci terraform-docs
```

It is also possible to run existing script in your repository, set env var and auto remove the container:

```bash
docker run --rm -ti -e CI_DOCTOC=1 -v "$PWD:/work" claranet/terraform-ci:latest ./scripts/module/loop_wrapper.sh ./scripts/module/gen_doc.sh
```

## Dev

Be sure docker service is run and:

```bash
docker build -t claranet/terraform-ci .
docker tag claranet/terraform-ci  claranet/terraform-ci:0.13.5
docker push claranet/terraform-ci:0.13.5
```


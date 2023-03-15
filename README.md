# DenoiseIt Linux Container

[![Container Build](https://github.com/Procsiab/denoiseit-lc/actions/workflows/build-container-publish-dockerhub.yaml/badge.svg)](https://github.com/Procsiab/denoiseit-lc/actions/workflows/build-container-publish-dockerhub.yaml)

![Docker Image Version (latest by date)](https://img.shields.io/docker/v/procsiab/denoiseit-lc?label=Latest%20tag%20pushed%20on%20Docker%20Hub)

A Linux Container minimal distribution based on Alpine, to make available the compiled binary of the DenoiseIt project.

An AUR package maintained by the author is publicly available, however a Linux Container is best suited to integrate this handy tool into a pipeline.

Thank You **DragoonAethis**, for putting together DenoiseIt!

Find out more on the author's [repo](https://github.com/DragoonAethis/DenoiseIt).

## Build the Container Image

Run the following command in this repo's directory:

```bash
podman build -f Containerfile.amd64 -t mycompany/denoiseit-lc .
```

**NOTE**: You may want to use the `Containerfile.aarch64` to produce an image for an ARM64v8 target platform. In that case, the QEMU AARCH64 static binary (from package `qemu-user-static`) must be installed on the system.

**NOTE**: You can choose which commit of the author's repo use as a codebase for the build process, passing the argument `--build-arg=commit=<SHA>` to the `podamn build` command. Replace *<SHA>* with the hash of the designed commit.

## Run the Image

From the folder you want to share inside the container, run the following command (replace `podman` with `docker` if using the latter):

```bash
podman run --rm -it -v:$(pwd):/workdir:Z docker.io/procsiab/denoiseit-lc:v1.0-amd64
```

You will be presented with a Bash prompt, inside the shared folder you chose with the `-v` argument.

### Prepare the audio for DenoiseIt

At the time of writing this, I find myself using a lot the following FFMPEG command to prepare a video file to be consumed by the `denoiseit` command: its input must be a WAV file, with a sample rate of 48KHz; therefore it seems you cannot feed it directly a container file (e.g. MP4) with audio and video streams in it.

In short, run the following command on your *input.mp4* file to extract the audio and make it readable from `denoiseit`:
```bash
ffmpeg -i input.mp4 -acodec pcm_s16le -ac 2 -af "aresample=48000" denoiseit-input.wav
```

Afterwards, I stitch together the audio file obtained from `denoiseit` and the video stream from the original input file.

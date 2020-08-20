# Video Streamer Using Multiple Containers
This alwaysAI app performs realtime object detection and streams video and text data to a Flask-SocketIO server running in another container managed by [Docker Compose](https://docs.docker.com/compose/).

## Setup
This app requires an alwaysAI account. Head to the [Sign up page](https://www.alwaysai.co/dashboard) if you don't have an account yet. Follow the instructions to install the alwaysAI toolchain on your development machine.

Next, create an empty project to be used with this app. When you clone this repo, you can run `aai app configure` within the repo directory and your new project will appear in the list.

This app can run on any docker-enabled Linux system (amd64, armv7hf, aarch64) with docker-compose installed.

## Architecture

This app has two components, a computer vision alwaysAI app and a server for displaying the video and data feed from the computer vision app. Docker compose is used to construct the app from the two services running in their own containers. The `docker-compose.yml` file defines the app structure and the arguments for each container.

## Usage

Before you can build and run the full app you need to install the dependencies of the alwaysAI computer vision app:

```
$ cd cv
$ aai app configure
$ aai app install
```

Now, you can build, start, and stop the app using `docker-compose`. Move back to the root app directory and run:

```
$ docker-compose build
$ docker-compose up
```

A link for the web page will be shown in the logs:

```
$ docker-compose up
Recreating video-streamer-multi-container_server_1 ... done
Recreating video-streamer-multi-container_cv_1     ... done
Attaching to video-streamer-multi-container_server_1, video-streamer-multi-container_cv_1
server_1  | [INFO] Starting server at http://localhost:5001
cv_1      | Loaded model:
cv_1      | alwaysai/mobilenet_ssd
cv_1      |
cv_1      | Engine: Engine.DNN
cv_1      | Accelerator: Accelerator.GPU
cv_1      |
cv_1      | Labels:
cv_1      | ['background', 'aeroplane', 'bicycle', 'bird', 'boat', 'bottle', 'bus', 'car', 'cat', 'chair', 'cow', 'diningtable', 'dog', 'horse', 'motorbike', 'person', 'pottedplant', 'sheep', 'sofa', 'train', 'tvmonitor']
cv_1      |
cv_1      | [INFO] Connecting to server http://localhost:5001...
cv_1      | [INFO] Successfully connected to server.
server_1  | [INFO] CV client connected: 20b8af7e927144dfac4f080f3946f661
server_1  | [INFO] Web client connected: 42fc37b5674f4613b2985d644cedfd2e
```

### Running the CV App in Standalone Mode

The CV app can also be run in Standalone mode using the Streamer. From the `cv` directory run the following commands:

```
$ aai app configure
$ aai app install
$ aai app start -- --use-streamer
```

> Note that tha extra `--` in the above commands is used to indicate that the parameters that follow are to be passed through to the python app, rather than used by the CLI.

### Changing the CV app arguments

The app has the following arguments:

```
$ aai app start -- --help
usage: app.py [-h] [--camera CAMERA] [--use-streamer]
              [--server-addr SERVER_ADDR] [--stream-fps STREAM_FPS]

alwaysAI Video Streamer

optional arguments:
  -h, --help            show this help message and exit
  --camera CAMERA       The camera index to stream from.
  --use-streamer        Use the embedded streamer instead of connecting to the
                        server.
  --server-addr SERVER_ADDR
                        The IP address or hostname of the SocketIO server
                        (Default: localhost).
  --stream-fps STREAM_FPS
                        The rate to send frames to the server in frames per
                        second (Default: 20.0).
```

To change the parameters that are used by docker compose, update the `CMD` line of the `Dockerfile.service` file in the `cv` directory. For example, to lower the streaming FPS:

```
CMD ["python3", "-u", "app.py", "--stream-fps", "5"]
```

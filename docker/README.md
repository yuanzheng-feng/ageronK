
# Hands-on Machine Learning in Docker

This is the Docker configuration which allows you to run and tweak the book's notebooks without installing any dependencies on your machine!<br/>
OK, any except `docker` and `docker-compose`.

## Prerequisites

Follow the instructions on [Install Docker](https://docs.docker.com/engine/installation/) and [Install Docker Compose](https://docs.docker.com/compose/install/) for your environment if you haven't got `docker` and `docker-compose` already.

Some general knowledge about `docker` infrastructure might be useful (that's an interesting topic on its own) but is not strictly *required* to just run the notebooks.

## Usage

### Prepare the image (once)

The first option is to pull the image from Docker Hub (this will download over 6 GB of data):

```bash
$ docker pull ageron/handson-ml2
$ docker tag ageron/handson-ml2 handson-ml2
```

Alternatively, you can build the image yourself. This will be slower, but it will ensure the image is up to date, with the latest libraries. For this, assuming you already downloaded this project into the directory `/path/to/project/handson-ml2`:

```bash
$ cd /path/to/project/handson-ml2/docker
$ docker-compose build
```

This will take quite a while, but is only required once.

After the process is finished you have a `handson-ml2` image, that will be the base for your experiments. You can confirm that by running the following command:

```bash
$ docker images
REPOSITORY        TAG                 IMAGE ID            CREATED             SIZE
handson-ml2       latest              6c4dc2c7c516        2 minutes ago       6.49GB
```

### Run the notebooks

Still assuming you already downloaded this project into the directory `/path/to/project/handson-ml2`, run the following commands to start the Jupyter server inside the container (it is also named `handson-ml2`, just like the image):

```bash
$ cd /path/to/project/handson-ml2/docker
$ docker-compose up
```

Next, just point your browser to the URL printed on the screen (or go to <http://localhost:8888> if you enabled password authentication inside the `jupyter_notebook_config.py` file, before building the image) and you're ready to play with the book's code!

The server runs in the directory containing the notebooks, and the changes you make from the browser will be persisted there.

You can close the server just by pressing `Ctrl-C` in terminal window.

### Using `make` (optional)

If you have `make` installed on your computer, you can use it as a thin layer to run `docker-compose` commands. For example, executing `make rebuild` will actually run `docker-compose build --no-cache`, which will rebuild the image without using the cache. This ensures that your image is based on the latest version of the `continuumio/miniconda3` image which the `handson-ml2` image is based on.

If you don't have `make` (and you don't want to install it), just examine the contents of `Makefile` to see which `docker-compose` commands you can run instead.

### Run additional commands in the container

Run `make exec` (or `docker-compose exec handson-ml2 bash`) while the server is running to run an additional `bash` shell inside the `handson-ml2` container. Now you're inside the environment prepared within the image.

One of the useful things that can be done there would be starting TensorBoard (for example with simple `tb` command, see bashrc file).

Another one may be comparing versions of the notebooks using the `nbdiff` command if you haven't got `nbdime` installed locally (it is **way** better than plain `diff` for notebooks). See [Tools for diffing and merging of Jupyter notebooks](https://github.com/jupyter/nbdime) for more details.

You can see changes you made relative to the version in git using `git diff` which is integrated with `nbdiff`.

You may also try `nbd NOTEBOOK_NAME.ipynb` command (custom, see bashrc file) to compare one of your notebooks with its `checkpointed` version.<br/>
To be precise, the output will tell you *what modifications should be re-played on the **manually saved** version of the notebook (located in `.ipynb_checkpoints` subdirectory) to update it to the **current** i.e. **auto-saved** version (given as command's argument - located in working directory)*.

## GPU Support on Linux (experimental)

If you're using Linux, and you have a TensorFlow-compatible GPU card (NVidia card with Compute Capability ≥ 3.5) that you would like TensorFlow to use inside the docker container, then you should download and install the latest driver for your card from [nvidia.com](https://www.nvidia.com/Download/index.aspx?lang=en-us). You will also need to install [NVidia Docker support](https://github.com/NVIDIA/nvidia-docker): if you are using Docker 19.03 or above, you must install the `nvidia-container-toolkit` package, and for earlier versions, you must install `nvidia-docker2`.

To build the image, edit `docker-compose.yml`, replace the line `dockerfile: ./docker/Dockerfile` with `dockerfile: ./docker/Dockerfile.gpu`, and then run:

```bash
$ cd /path/to/project/handson-ml2/docker
$ docker-compose build
```

To run the image, it's depends. If you have `docker-compose` version 1.28 or above, that's great! You can simply uncomment the `deploy` section in `docker-compose.yml`, and then run:

```bash
$ cd /path/to/project/handson-ml2/docker
$ docker-compose up
[...]
     or http://127.0.0.1:8888/?token=[...]
```

However, if you have an earlier version of `docker-compose`, it's simpler to use `docker run` directly. If you are using Docker 19.03 or above, you can run:

```bash
$ cd /path/to/project/handson-ml2
$ docker run --name handson-ml2 --gpus all -p 8888:8888 -p 6006:6006 --log-opt mode=non-blocking --log-opt max-buffer-size=50m -d -v `pwd`:/home/devel/handson-ml2 handson-ml2 /opt/conda/envs/tf2/bin/jupyter notebook --ip='0.0.0.0' --port=8888 --no-browser
```

If you are using an older version of Docker, then replace `--gpus all` with `--runtime=nvidia`.

Then, display the container's logs and point your browser to the URL printed on the screen:

```bash
$ docker logs handson-ml2
[I 09:07:10.805 NotebookApp] Writing notebook server cookie secret to /home/devel/.local/share/jupyter/runtime/notebook_cookie_secret
[...]
     or http://127.0.0.1:8888/?token=[...]
```

If everything goes well, Jupyter should appear, and if you open a notebook and execute the following code, it should show a GPU device in the list:

```python
import tensorflow as tf

tf.config.list_physical_devices()
```

Lastly, to stop and destroy the container (but not the image), run:

```bash
$ docker stop handson-ml2
$ docker rm handson-ml2
```

FROM continuumio/miniconda:4.7.12

# https://askubuntu.com/questions/141928/what-is-the-difference-between-bin-sh-and-bin-bash
# bash has more functionality than bin, such as "source"
SHELL [ "/bin/bash", "--login", "-c" ]

# Must match conda location in base Dockerfile:
# https://github.com/ContinuumIO/docker-images/blob/master/miniconda/debian/Dockerfile
ENV CONDA_DIR opt/conda

# make non-activate conda commands available
ENV PATH $CONDA_DIR/bin:$PATH

# Conda env installation and setup based on code from:
# https://github.com/kaust-vislab/tensorflow-gpu-data-science-project/blob/0ef82814ec1cc00c1817d2fed4328fcec885f647/docker/Dockerfile
ENV PROJECT_DIR $HOME/app
RUN mkdir $PROJECT_DIR
WORKDIR $PROJECT_DIR

# build the conda environment
ENV ENV_NAME env

RUN cat ~/.bashrc
RUN echo $PATH

RUN conda create -n $ENV_NAME python=3.6 && \
    # https://automl.github.io/auto-sklearn/master/installation.html#installing-auto-sklearn
    conda install -n $ENV_NAME gxx_linux-64 gcc_linux-64 swig

COPY ./conda_jupyter_notebook/environment.yml ./environment.yml

# make conda activate command for $ENV_PREFIX environment available from /bin/bash --interactive shells
RUN echo "source activate $ENV_NAME" > ~/.bashrc
# RUN echo "pushd $CONDA_DIR/bin && source activate $ENV_NAME && popd" > ~/.bashrc

ENV PATH /opt/conda/envs/$ENV_NAME/bin:$PATH

RUN conda update --name base --channel defaults conda && \
    conda env update -n $ENV_NAME --file ./environment.yml && \
    conda activate $ENV_NAME && \
    conda clean --all --yes

COPY . .

# Allow python to discover modules
ENV PYTHONPATH "${PYTHONPATH}:/app"

# Password to login to the jupyter notebook
ARG JUPYTER_PASSWORD_SHA
# Use ARG at container run time
ENV JUPYTER_PASSWORD_SHA $JUPYTER_PASSWORD_SHA

CMD ["bash", "-c", "jupyter notebook --ip 0.0.0.0 --allow-root \
--notebook-dir /home/jovyan/work"]
# --NotebookApp.password=$JUPYTER_PASSWORD_SHA"]

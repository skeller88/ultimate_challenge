# set JUPYTER_PASSWORD_SHA
source .bash/env_secrets

export FILEDIR=conda_jupyter_notebook
export IMAGE_NAME=$BASE_IMAGE_NAME/conda_jupyter_notebook

docker build -t $IMAGE_NAME --file $FILEDIR/Dockerfile --build-arg JUPYTER_PASSWORD_SHA=$JUPYTER_PASSWORD_SHA .
docker run -it --rm -p 8888:8888 \
--volume ~:/home/jovyan/work \
--env-file $FILEDIR/env.list $IMAGE_NAME

docker push $IMAGE_NAME

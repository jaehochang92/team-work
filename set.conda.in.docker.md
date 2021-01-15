### How git works?
```bash
mkdir git-ws
cd git-ws
git clone URL

git checkout new-branch
git status

git pull
git add.
git commit -m ‘commit messages’
git push origin new-branch
```

### 1. To obtain a fully working Anaconda image:
```bash
docker search continuumio
```
> Pull the desired image:
```bash
docker pull continuumio/miniconda3
```
> Create a container using the image:
```bash
docker run -t -i continuumio/miniconda3 /bin/bash
```
> Test the container:
```bash
conda info
```
> You now have a fully working Anaconda image. Hit ctrl-D to exit the bash shell.

> If you want to install and launch the Jupyter Notebook, execute the following command all on one line from the host machine:
```bash
docker run -i -t -p 8888:8888 continuumio/miniconda /bin/bash -c “/opt/conda/bin/conda install jupyter -y --quiet && mkdir /opt/notebooks && /opt/conda/bin/jupyter notebook --notebook-dir=/opt/notebooks --ip=‘*’ --port=8888 --no-browser”
```

### 2. Deploy your own container with miniconda image
> Use the following code to make a file named `Dockerfile`.
```Dockerfile
FROM continuumio/miniconda

ADD [yml 경로] /tmp/environment.yml
RUN conda init
RUN conda env create -f /tmp/environment.yml

# Pull the environment name out of the environment.yml
RUN echo "source activate $(head -1 /tmp/environment.yml | cut -d' ' -f2)" > ~/.bashrc
ENV PATH /opt/conda/envs/$(head -1 /tmp/environment.yml | cut -d' ' -f2)/bin:$PATH
```
> Deploy a container using `Dockerfile`. This command should run in the directory where your `Dockerfile` and `yml` is.
```bash
docker build -t remote-conda .
```

### 3. Environment management using `conda env`

> Create conda env from yml without OS conflict issues
```bash
conda env create -f [yml 경로] -n [환경이름]
```

> Deploy conda env kernel in Jupyter lab
```bash
conda activate [환경이름]
conda install ipykernel
ipython kernel install --user --name=[커널이름]
conda deactivate

jupyter lab --ip=[ip address] --port=8888 --allow-root
```

> Export conda environment as `yml`
```bash
conda env export -f my-env.yml --no-builds  # --no-builds option prevents platform conflicts
```
caution: You may see the path of the environment given in the last line of `*.yml` file and it depends on your work. So it may need a change.
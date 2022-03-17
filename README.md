Thanks to [Freed-Wu/my-dockerfile](https://github.com/Freed-Wu/my-dockerfile) and [vid2e](https://github.com/uzh-rpg/rpg_vid2e.git) source code.
# Update
- bitahub_nv111/ contain env vid2e, and include torch1.10.2 cuda11.1 esim_py
- bitahub_nv113/ contain env vid2e, and include torch1.11 cuda11.3, install from conda
- both need to build Dockerfile, and then run the image -> install esim_torch/ manually -> commit the container to image.

# Steps to run vid2e code on bitahub
## 1 Build dockerfile_bitahub on [Bitahub](https://www.bitahub.com/login)
## 2 Open the terminnal and create the vid2e virtual environment
```bash
source activate && \
conda activate vid2e && \
pip install torch==1.3.1 torchvision==0.4.2 \
```
Maybe the higher version of torch, torchvision is better
```bash
pip3 install torch==1.10.2+cu113 torchvision==0.11.3+cu113 torchaudio==0.10.2+cu113 -f https://download.pytorch.org/whl/cu113/torch_stable.html && \ 
conda install -y -c conda-forge pybind11 matplotlib && \
pip install /home/liam/rpg_vid2e/esim_py/ && \
pip install /home/liam/rpg_vid2e/esim_torch/ && \
pip install opencv-python-headless
```
## 3 Usage
This part is same as [vid2e](https://github.com/uzh-rpg/rpg_vid2e) Example part.
```bash
#clone vid2e reposity to /code/rpg_vid2e/
git clone https://github.com/uzh-rpg/rpg_vid2e.git /code/rpg_vid2e/
cd /code/rpg_vid2e/
device=cuda:0
python upsampling/upsample.py --input_dir=example/original --output_dir=example/upsampled --device=$device
python esim_torch/generate_events.py --input_dir=example/upsampled \
                                     --output_dir=example/events \
                                     --contrast_threshold_neg=0.2 \
                                     --contrast_threshold_pos=0.2 \
                                     --refractory_period_ns=0
```
# Other
Due to Bitahub host machine doesn't have GPU, the file Dockerfile_total needs to be built on GPU machine. Then it's no need to install vid2e conda environment.
```bash
docker build -t pytorch/vid2e:1.3-cuda10.1-cudnn7-devel .
docker run -it pytorch/vid2e:1.3-cuda10.1-cudnn7-devel /bin/bash
```

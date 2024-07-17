# Fast-3D-Talking-Face: Blendshape-based Audio-Driven 3D-Talking-Face with Transformer


## Environment

Create conda environment 
```
conda create -n talking_face python=3.9.18
conda activate talking_face
pip install torch==1.12.1+cu113 torchvision==0.13.1+cu113 torchaudio==0.12.1 --extra-index-url https://download.pytorch.org/whl/cu113
pip install -r requirements.txt
```

## Create BlendVOCA

### Construct Blendshape Facial Model

Due to the license issue of VOCASET, we cannot distribute BlendVOCA directly.
Instead, you can preprocess `data/blendshape_residuals.pickle` after constructing `BlendVOCA` directory as follows for the simple execution of the script.

```bash
BlendVOCA
   └─ templates
      ├─ ...
      └─ FaceTalk_170915_00223_TA.ply
```

- `templates`: Download the template meshes from [VOCASET](https://voca.is.tue.mpg.de/download.php).

```bash
python preprocess_blendvoca.py
```

### Generate Blendshape Coefficients

If you want to generate coefficients by yourself, we recommend constructing the `BlendVOCA` directory as follows for the simple execution of the script.

```bash
BlendVOCA
  ├─ blendshapes_head
  │  ├─ ...
  │  └─ FaceTalk_170915_00223_TA
  │     ├─ ...
  │     └─ noseSneerRight.obj
  ├─ templates_head
  │  ├─ ...
  │  └─ FaceTalk_170915_00223_TA.obj
  └─ unposedcleaneddata
     ├─ ...
     └─ FaceTalk_170915_00223_TA
        ├─ ...
        └─ sentence40
```

- `blendshapes_head`: Place the constructed blendshape meshes (head).
- `templates_head`: Place the template meshes (head).
- `unposedcleaneddata`: Download the mesh sequences (unposed cleaned data) from [VOCASET](https://voca.is.tue.mpg.de/download.php).

And then, run the following command:

```bash
python optimize_blendshape_coeffs.py
```
This step will take about 2 hours.

## Training / Evaluation on BlendVOCA

### Dataset Directory Setting

We recommend constructing the `BlendVOCA` directory as follows for the simple execution of scripts.

```bash
BlendVOCA
  ├─ audio
  │  ├─ ...
  │  └─ FaceTalk_170915_00223_TA
  │     ├─ ...
  │     └─ sentence40.wav
  ├─ bs_npy
  │  ├─ ...
  │  └─ FaceTalk_170915_00223_TA01.npy
  │    
  ├─ blendshapes_head
  │  ├─ ...
  │  └─ FaceTalk_170915_00223_TA
  │     ├─ ...
  │     └─ noseSneerRight.obj
  └─ templates_head
     ├─ ...
     └─ FaceTalk_170915_00223_TA.obj
```

- `audio`: Download the audio from [VOCASET](https://voca.is.tue.mpg.de/download.php).
- `bs_npy`: Place the constructed blendshape coefficients.
- `blendshapes_head`: Place the constructed blendshape meshes (head).
- `templates_head`: Place the template meshes (head).

### Training

    ```bash
    python main.py
    ```

### Evaluation

1. Prepare Unreal Engine5(test on UE5.1 and UE5.3) metahuman project
  1.1 Create default metahuman project in UE5
  1.2 Move [jsonlivelink](https://drive.google.com/drive/my-drive?hl=zh-cn) plugin into the Plugins of UE5 Animation
  1.3 Revise the blueprint of the face animation to cancel the default animation and rebuild
  1.4 Start jsonlivelink
  1.5 Run the level

2. Start the audio2face server, you can check your model under BlendVOCA:
  ```bash
  python audio2face_server.py --model_name save_512_xx_xx_xx_xx/100_model
  ```
3. Drive the metahuman Unreal Engine:

    ```bash
    cd metahuman_demo
    python demo.py --audio2face_url http://0.0.0.0:8000 --wav_path ../test/wav/speech_long.wav --livelink_host 0.0.0.0 --livelink_port 1234
    ```
  Since I deploy the metahuman project on my windows PC, so the livelink_host should be my PC's IP.

## Reference

- [SAiD](https://github.com/yunik1004/SAiD)

```text
@misc{park2023said,
      title={SAiD: Speech-driven Blendshape Facial Animation with Diffusion},
      author={Inkyu Park and Jaewoong Cho},
      year={2023},
      eprint={2401.08655},
      archivePrefix={arXiv},
      primaryClass={cs.CV}
}
```
- [SelfTalk_Release](https://github.com/psyai-net/SelfTalk_release)

```text
  @inproceedings{peng2023selftalk,
    title={SelfTalk: A Self-Supervised Commutative Training Diagram to Comprehend 3D Talking Faces}, 
    author={Ziqiao Peng and Yihao Luo and Yue Shi and Hao Xu and Xiangyu Zhu and Hongyan Liu and Jun He and Zhaoxin Fan},
    journal={arXiv preprint arXiv:2306.10799},
    year={2023}
  }
```
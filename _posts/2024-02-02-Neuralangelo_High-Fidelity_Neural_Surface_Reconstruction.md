---
title: Neuralangelo, High-Fidelity Neural Surface Reconstruction
date: 2024-02-02 11:20:00 +09:00
categories: [Papers]
tags: [3D Image Reconstruction, Machine Learning]
---

이 논문에서 소개하는 기술은 사진과 같은 이미지로부터, 복잡한 3D 모델을 생성하는 기술입니다.

![Neuralangelo: High-Fidelity Neural Surface Reconstruction teaser](https://github.com/NVlabs/neuralangelo/raw/main/assets/teaser.gif)

## Instructions

기존의 `sparse 3D reconstruction`은 반복적인 질감패턴을 갖는 모호한 관측 데이터를 처리하는 데 어려움이 있었습니다. `NeuS`와 같은 MLP를 적용함으로써 reconstruction의 정확도를 향상시킬 수 있었지만, 그 정확도는 크기에 비례하여 개선되지 않았습니다. 따라서 `Instant NGP`와 같은 더 효율적인 방법들이 제안되었습니다.

`Neuralangelo`는 `Instant NGP`를 MLP에 적용하여 성능향상을 기대하고 있습니다. 이를 위해 `Instant NGP`를 더욱 효율적으로 만드는 2가지 개선 사항을 제안하였습니다. 하나는 `Surface Reconstruction`이고 다른 하나는 `Optimization`입니다.

`Neuralangelo`의 핵심 아이디어는 `Multi-resolution hash encoding`을 자연스럽게 통합하는 프레임워크를 제공하고, `Surface Reconstruction` 품질을 향상시키는 두 가지 기술을 제안하는 것입니다.

## Related Works

### Neural Radiance Fields (NeRF)

![NeRF Volume Rendering](https://i.imgur.com/HHb6tlQ.png)

카메라의 위치에 따라 발생하는 광선(ray)을 이용하여, Volume Rendering 방식을 통해 이 광선을 따라 추출된 샘플 포인트들의 색채(color radiance)를 종합하여 3차원 장면(3D scene)을 볼륨 밀도(Volume density)와 색채 필드(color fields)로 표현합니다.


### Neural surface reconstruction (NeuS)

![NeuS reconstruction](https://vcai.mpi-inf.mpg.de/projects/NeuS2/images/1_fig_method.jpg)

NeRF의 Volume Rendering을 SDF 표현으로 변환하는 방법을 제안합니다. NeRF는 volume density만을 학습하기 때문에 고품질의 표면(High-quality surface)을 얻는 것이 어렵습니다. 따라서 표면(Surface) 표현을 위해 SDF를 사용하고, SDF representation을 학습하기 위해 NeRF와 마찬가지로 Volume Rendering을 활용하여 성능을 개선했습니다.


### Instant NGP

![Instant NGP](https://blog.kakaocdn.net/dn/2cHsX/btsl5i564Y5/VkzK1v6BJkykKkKERSqo30/img.png)

> Instant NGP는 최근 NVIDIA에서 제안한 방법으로, 기존의 NeRF(Neural Radiance Fields)보다 훨씬 빠른 속도로 고품질 3D 장면을 재구성할 수 있는 기술입니다.

기존 NeRF에서 사용하던 위치 인코딩(positional encoding)은 NeRF의 속도를 저하시키는 원인이었습니다. 반면 voxel-based 방법은 속도는 빠르지만 대량의 메모리가 필요하다는 단점이 있었습니다. 이에 따라 Instant NGP에서는 NeRF의 위치 인코딩을 개선한 multi-resolution hash encoding을 제안하였습니다. 이는 voxel-based 방법을 사용하여 속도를 향상시키고, hash function을 사용하여 메모리 사용을 줄이는 방법입니다.

## Approach
![Neuralangelo: High-Fidelity Neural Surface Reconstruction poster from Nvidia research](https://research.nvidia.com/labs/dir/neuralangelo/poster.png)

### 적용 방법
1. 카메라 뷰 방향에 따라서 3D 위치를 다양한 해상도로 샘플링 해서 hash Encoding 한 Encoded feature 생성
2. Encoded feature는 두 개의 별도의 신경망에 공급된다
    - SDF 예측
    - 색상 예측
3. 예측 결과를 SDF 기반 볼륨 렌더링 기법과 결합

### Numerical Gradient Computation

SDF 예측에는 `eikonal loss`를 사용하며, 이 손실 함수는 gradient가 `eikonal 방정식`을 만족하며 유효한 `SDF`라는 것을 증명합니다. 이때 사용되는 gradient에는 빠른 대신 에러가 있는 `Analytic gradient`와 느리지만 정확한 `Numerical gradient`가 있습니다.

`Neus`는 `Analytic gradient`를 사용했지만, 이는 local grid에서만 최적화가 이루어졌기 때문에 non-local smoothness가 부족하다는 문제가 있습니다. 그래서 `Numerical gradient`를 사용하여 local cell을 넘어 최적화할 수 있도록 하였습니다. 이때, 해시 그리드의 공간 해상도와 일치하는 단계 크기의 `Numerical gradient`를 사용하여 더욱 효과적인 최적화를 이룰 수 있습니다.

### Progressive Levels of Details

`Neuralangelo`는 점직적으로 세부 정보를 잘 표현하기 위해 `coarse-to-fine optimization`를 사용합니다.

고차 도함수(high-order derivatives)에 대해 `numerical gradient`를 사용하는 것은 `neuralangelo`가 두 가지 관점에서 `coarse-to-fine optimization`를 수행할 수 있게 해줍니다. 첫째, `step size ε`은 수치적 기울기가 해상도와 복구된 세부 정보의 양을 제어하는 방식으로 해석될 수 있습니다. 큰 `ε`을 사용하면 더 큰 스케일에서 일관된 연속적인 표면을 생성하고, 작은 `ε`을 사용하면 작은 영역에서 세부정보를 부드럽게 처리할 수 있습니다. 둘째, `Hash grid resolution V`는 처음에는 거친 해시 격자만 최적화하고, `ε`가 공간 크기에 도달할 때까지 점차적으로 미세한 해시 격자를 최적화합니다.

## Experiment

평균적으로 `neuralangelo`는 보조 입력(세그멘테이션, 깊이)을 사용하지 않아도 가장 낮은 챔퍼 거리(lowest chamfer distance)와 가장 높은 PSNR을 달성할 수 있습니다. 이는 `neuralangelo`가 모든 개별 장면에서 최상의 성능을 발휘하지는 않지만, 표면 복구와 이미지 합성에 있어서 이전의 작업들보다 더 일반적이고 우수한 성능을 보여준다는 것을 의미합니다.

## Conclusion

`Neuralangelo`는 고계도함수를 위한 `numerical gradient`와 `coarse-to-fine optimization`을 사용하고, 다중 해상도 해시 인코딩의 표현력을 활용하여 SDF로 모델링된 신경 표면 재구성을 실현합니다. 이를 통해 오브젝트 중심의 캡처와 대규모 실내/외 장면의 조밀한 구조를 매우 높은 충실도로 효과적으로 복구하여, RGB 비디오에서 상세한 대규모 장면을 재구성할 수 있음을 보여주었습니다.

## 참고

- [Neuralangelo: High-Fidelity Neural Surface Reconstruction (Paper in arxiv)](https://arxiv.org/abs/2306.03092)
- [Neuralangelo: High-Fidelity Neural Surface Reconstruction (Nvidia Research)](https://research.nvidia.com/labs/dir/neuralangelo/)
- [Neuralangelo: High-Fidelity Neural Surface Reconstruction (Github Code)](https://github.com/NVlabs/neuralangelo)
- [Instant Neural Graphics Primitives with a Multiresolution Hash Encoding](https://nvlabs.github.io/instant-ngp/)
- [NeuS: Learning Neural Implicit Surfaces by Volume Rendering for Multi-view Reconstruction](https://lingjie0206.github.io/papers/NeuS/)
- [Neuralangelo: High-Fidelity Neural Surface Reconstruction (박정현님)](https://youtu.be/rvNCcXe3trs)
- [Neuralangelo: High-Fidelity Neural Surface Reconstruction (김유지님)](https://www.youtube.com/watch?v=5sEYfiv_AOw)
- [모두의 연구소](https://modulabs.co.kr/blog/nerf-from-2d-to-3d/)
- [NeuS2](https://vcai.mpi-inf.mpg.de/projects/NeuS2/)

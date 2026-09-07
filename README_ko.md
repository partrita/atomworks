[![Ruff](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/astral-sh/ruff/main/assets/badge/v2.json)](https://github.com/astral-sh/ruff)
[![PyPI version](https://img.shields.io/pypi/v/atomworks.svg)](https://pypi.org/project/atomworks/)
[![Python versions](https://img.shields.io/pypi/pyversions/atomworks.svg)](https://pypi.org/project/atomworks/)
[![Documentation Status](https://img.shields.io/badge/docs-latest-brightgreen.svg)](https://rosettacommons.github.io/atomworks/latest/)
[![License: BSD 3-Clause](https://img.shields.io/badge/License-BSD%203--Clause-blue.svg)](https://opensource.org/licenses/BSD-3-Clause)

<div align="center">
  <img src="docs/_static/atomworks_logo_color.svg" width="450" alt="atomworks logo">
</div>

**atomworks**는 생체 분자 모델링 작업을 위한 연구 속도를 극대화하는 오픈 소스 플랫폼입니다. [Torchvision](https://docs.pytorch.org/vision/stable/index.html)이 비전 도내에서, [Torchaudio](https://docs.pytorch.org/audio/main/)가 오디오 도메인 내에서 신속한 프로토타이핑을 가능하게 하는 것처럼, AtomWorks는 생체 분자 모델링 내에서 개발 및 실험을 가속화하는 것을 목표로 합니다.

> **⚠️ 알림:** 현재 리포지토리 내에서 일부 정리 작업을 마무리하고 있습니다. 다음 일주일 내에 API(예: 함수 및 클래스 이름, 입력 및 출력)가 안정화될 것으로 예상됩니다. 기다려 주셔서 감사합니다!

기본 프레임워크가 아닌 AtomWorks와 통합되는 모델 자체(예: RF3, MPNN)를 찾고 있다면 [ModelForge](https://github.com/RosettaCommons/modelforge)를 확인하세요.

> **💡 참고:** 어디서부터 시작해야 할지 모르겠나요? [AtomWorks 문서의 예제](https://rosettacommons.github.io/atomworks/latest/auto_examples/index.html)에 몇 가지 유용한 시나리오를 통해 작업하는 방법을 만들어 두었습니다. 전체 튜토리얼은 현재 제작 중입니다!

AtomWorks는 두 가지의 상호 의존적인 라이브러리로 구성되어 있습니다:

- `atomworks.io`: 생물학적 데이터(구조, 서열, 저분자)를 파싱, 정리, 조작 및 변환하기 위한 범용 Python 툴킷입니다. [biotite](https://www.biotite-python.org/) API를 기반으로 구축되었으며, mmCIF, PDB, FASTA, SMILES, MOL 등과 같은 표준 형식 간에 원활하게 로드하고 내보낼 수 있습니다. 생체 분자 구조 데이터를 다루는 모든 사람에게 널리 유용합니다.
- `atomworks.ml`: `atomworks.io`를 구조적 중추로 사용하는 딥러닝 워크플로우를 위한 고급 데이터셋 특성 추출(featurization) 및 샘플링을 제공합니다. 일반적인 작업을 위해 미리 구축되고 잘 테스트된 포괄적인 `Transforms` 세트를 제공하며, 이를 통해 전체 딥러닝 파이프라인을 쉽게 구성할 수 있습니다. 사용자는 사용자 정의 작업을 위해 자신만의 `Transforms`를 만들 수도 있습니다.

AtomWorks의 동기 및 응용 프로그램에 대한 자세한 내용은 [preprint](https://doi.org/10.1101/2025.08.14.670328)를 참조하세요.

AtomWorks는 [biotite](https://www.biotite-python.org/) 위에 구축되었습니다: 이렇게 고품질의 유연한 툴킷을 유지 관리해 주시는 Biotite 개발자분들께 감사드리며, 저희 패키지가 더 넓은 `biotite` 커뮤니티에 도움이 되기를 바랍니다.

---

## atomworks.io

> *생체 분자 구조 파일을 정리, 표준화 및 조작하기 위한 범용 Python 툴킷 - [biotite](https://www.biotite-python.org/) 기반 구축:

**atomworks.io**를 사용하면 다음을 수행할 수 있습니다:

- 일반적인 생물학적 파일(구조 또는 서열)을 파싱, 변환 및 정리합니다. 예를 들어, 이탈기(leaving groups) 식별 및 제거, 친핵성 첨가 후 결합 차수 수정, 전하 수정, 공유 기하구조 파싱, 대칭 중심에서의 다중 점유 및 리간드 구조에 대한 적절한 처리 등을 수행할 수 있습니다.
- 초기 소스에 관계없이 추가 분석 또는 머신 러닝 애플리케이션을 위해 모든 데이터를 일관된 `AtomArray` 표현으로 변환합니다.
- 누락된 원자(서열에는 암시되어 있지만 좌표에는 표현되지 않은 원자)를 모델링하고 엔터티 및 인스턴스 수준의 주석을 초기화합니다(구성 가능한 명명 규칙에 대한 자세한 내용은 [용어 사전](https://rosettacommons.github.io/atomworks/latest/glossary.html) 참조).

우리는 `atomworks.io`가 광범위한 생물정보학 및 단백질 디자인 사용자들에게 일반적으로 유용하다는 것을 발견했습니다. 많은 경우 `atomworks.io`는 맞춤형 스크립트와 수동 큐레이션을 대체할 수 있어, 연구자들이 수십 개의 도구와 의존성을 다루는 대신 가설을 검증하는 데 더 많은 시간을 할애할 수 있게 해줍니다.

---

## atomworks.ml

> *생체 분자 딥러닝 워크플로우 내의 데이터셋 특성 추출을 위한 모듈식 컴포넌트 기반 라이브러리*

**atomworks.ml**은 다음을 제공합니다:

- 새로운 파이프라인에 끼워 넣을 수 있는 미리 구축되고 잘 테스트된 `Transforms` 라이브러리
- `atomworks.io`와 통합되어 임의의 사용 사례를 위한 `Transforms`를 작성할 수 있는 확장 가능한 프레임워크
- 대부분의 모델 학습 시나리오에 적합한 미리 구축된 데이터셋 및 샘플러

AtomWorks 패러다임 내에서 각 `Transform`의 출력은 모델별 텐서가 있는 불투명한 딕셔너리가 아니라, 원자 수준 구조 표현(Biotite의 `AtomArray`)의 업데이트된 버전입니다. 따라서 파이프라인 내부 및 파이프라인 간의 작업은 입력 및 출력의 공통 어휘를 유지합니다.

우리는 `atomworks.ml`이 많은 ML 프로젝트를 시작하고 완료하는 데 드는 오버헤드를 **획기적으로** 줄여준다는 것을 발견했습니다. 예전에는 몇 달이 걸리던 연구 주제가 이제는 며칠은 아니더라도 몇 주 안에 신호를 얻어 혁신의 속도를 가속화합니다.

---

## 언제 `atomworks.io` 대 `atomworks.ml`을 사용해야 하나요?

- 다음과 같은 경우 `atomworks.io`를 사용하세요:
  - 생물학적 파일 형식(mmCIF, PDB, FASTA 등) 간의 파싱/정리/변환이 필요한 경우
  - 다운스트림 분석 또는 모델링에 연결할 통합된 구조적 표현을 원하는 경우
  - 누락된 원자 추가, 리간드/용매 필터링 또는 어셈블리 생성과 같은 구조적 작업이 필요한 경우

- 다음과 같은 경우 `atomworks.ml`을 사용하세요:
  - 딥러닝을 위해 전체 데이터셋을 특성 추출(featurization)해야 하는 경우
  - 학습 파이프라인을 위한 기성 샘플링 및 배치 유틸리티를 원하는 경우
  - 이미 `atomworks.io`를 사용하고 있으며 ML 준비가 된 특성 엔지니어링으로의 원활한 연결이 필요한 경우

---

## 설치
> 참고: AtomWorks는 Python >= 3.11 및 [`dotenv`](https://pypi.org/project/python-dotenv/#file-format)가 필요합니다.

```shell
pip install atomworks # torch 없는 기본 설치 버전 (atomworks.io 전용)
pip install "atomworks[ml]" # torch 및 ML 의존성 포함 (atomworks.io 및 atomworks.ml 용)
pip install "atomworks[dev]" # 개발 의존성 포함
pip install "atomworks[openbabel]" # [Open Babel](https://openbabel.org/) 및 해당 의존성 포함
pip install "atomworks[ml,openbabel,dev]" # 모든 의존성 포함
```
*이러한 설치를 여러 번 실행하면 설치된 의존성이 추가될 뿐이며 atomworks가 중복 설치되지는 않습니다.*

패키지 관리에 [uv](https://docs.astral.sh/uv/reference/policies/versioning/)를 사용하는 경우 다음을 사용하여 atomworks를 설치할 수 있습니다:

```shell
uv pip install "atomworks[ml,openbabel,dev]"
```

더 고급 설정 옵션(apptainers를 통한 워크플로우 실행 방법 포함)은 [전체 문서](https://rosettacommons.github.io/atomworks/latest/index.html)를 참조하세요.

---

## 시작하기

이 섹션에는 atomworks를 설정하는 방법과 atomworks.io의 일부 기능을 사용하여 PDB 파일을 파싱하는 빠른 가이드가 포함되어 있습니다. atomworks.io 및 atomworks.ml의 기능에 대해 자세히 알아보려면 [외부 문서](https://rosettacommons.github.io/atomworks/latest/)를 참조하세요.

PDB 파일을 파싱(파싱 = 로드, 정리, 엔터티, 분자 등과 같은 관련 메타데이터 주석 달기)하려면 `parse` 함수를 사용할 수 있습니다:

> 참고: 이 섹션의 코드를 실행하려면 3nez.cif.gz 파일을 직접 다운로드해야 합니다. Python 스크립트 내에서 PDB 파일을 다운로드하는 방법은 [예제](https://rosettacommons.github.io/atomworks/latest/auto_examples/index.html)를 참조하세요.

```python

from atomworks.io.parser import parse
from biotite.structure import AtomArrayStack

result = parse(filename="3nez.cif.gz")

asym_unit: AtomArrayStack = result["asym_unit"]
assemblies: dict[str, AtomArrayStack] = result["assemblies"]

for chain_id, info in result["chain_info"].items():
    print(chain_id, info["processed_entity_canonical_sequence"])

```

`parse`의 출력에는 다음이 포함됩니다:

- **chain_info** — 각 체인에 대한 서열/메타데이터
- **ligand_info** — 리간드 주석 및 지표
- **asym_unit** — 구조 (`AtomArrayStack`)
- **assemblies** — 구축된 생물학적 어셈블리 (각각 고유한 `AtomArrayStack`임)
- **metadata** — 실험 및 소스 정보

`parse()` 사용에 대한 더 많은 예제는 [사용 예제](https://rosettacommons.github.io/atomworks/latest/auto_examples/index.html)를 참조하세요. 제공된 모든 예제는 이 메서드를 사용합니다.
이 메서드에 대한 자세한 내용은 [API 참조 문서](https://rosettacommons.github.io/atomworks/latest/io/parser.html)를 확인하세요.

단순히 파일을 로드하려면 `load_any` 함수를 사용할 수 있습니다:

```python
from atomworks.io.utils.io_utils import load_any
from biotite.structure import AtomArray

atom_array: AtomArray = load_any("3nez.cif.gz", model=1)  # model=1은 파일의 모든 모델 스택이 아닌 모델 1(즉, 첫 번째 모델)을 로드하려는 것을 의미합니다.
```

---

## 기여

개선 사항을 환영합니다!

기여 가이드라인은 [전체 문서의 기여 가이드](https://rosettacommons.github.io/atomworks/latest/contributor_guide.html)를 참조하세요.

## 감사의 말

코드베이스, 문서, 튜토리얼 및 예제에 대한 파트너십과 협력을 해주신 Rosetta Commons의 Hope Woods와 Rachel Clune에게 감사드립니다.

## 인용

연구에 AtomWorks를 사용하는 경우 다음을 인용해 주세요:

> N. Corley\*, S. Mathis\*, R. Krishna\*, M. S. Bauer, T. R. Thompson, W. Ahern, M. W. Kazman, R. I. Brent, K. Didi, A. Kubaney, L. McHugh, A. Nagle, A. Favor, M. Kshirsagar, P. Sturmfels, Y. Li, J. Butcher, B. Qiang, L. L. Schaaf, R. Mitra, K. Campbell, O. Zhang, R. Weissman, I. R. Humphreys, Q. Cong, H. Jiang, J. Funk, S. Sonthalia, P. Lio, D. Baker, F. DiMaio,
> "Accelerating Biomolecular Modeling with AtomWorks and RF3," bioRxiv, August 2025. doi: [10.1101/2025.08.14.670328](https://doi.org/10.1101/2025.08.14.670328)

bibtex를 사용하는 경우 Google Scholar 형식의 인용은 다음과 같습니다:

```bibtex
@article{corley2025accelerating,
  title={Accelerating Biomolecular Modeling with AtomWorks and RF3},
  author={Corley, Nathaniel and Mathis, Simon and Krishna, Rohith and Bauer, Magnus S and Thompson, Tuscan R and Ahern, Woody and Kazman, Maxwell W and Brent, Rafael I and Didi, Kieran and Kubaney, Andrew and others},
  journal={bioRxiv},
  pages={2025--08},
  year={2025},
  publisher={Cold Spring Harbor Laboratory}
}
```

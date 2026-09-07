설치
============

AtomWorks는 작업 흐름과 환경에 따라 여러 가지 방법으로 설치할 수 있습니다. 권장되는 방법은 다음과 같습니다:

0. 필수 조건
-----------------

AtomWorks를 설치하기 전에 다음 필수 조건이 갖춰져 있는지 확인하세요:

* Python 3.11 이상
* `dotenv <https://www.npmjs.com/package/dotenv>`_

1. pip를 통한 설치 (권장)
-----------------------------------
AtomWorks를 시작하는 가장 쉬운 방법입니다.

.. code-block:: bash

   pip install atomworks # torch 없는 기본 설치 버전 (atomworks.io 전용)
   pip install "atomworks[ml]" # torch 및 ML 의존성 포함 (atomworks.io 및 atomworks.ml 용)
   pip install "atomworks[dev]" # 개발 의존성 포함
   pip install "atomworks[ml,dev]" # 모든 의존성 포함

또한 RDKit의 대안인 `Open Babel <https://openbabel.org/>`_과 함께 AtomWorks를 설치할 수도 있습니다:

.. code-block:: bash

   pip install "atomworks[openbabel]"

또는 가능한 모든 의존성을 포함하여 설치하려면:

.. code-block:: bash

   pip install "atomworks[ml,openbabel,dev]"

Open Babel은 큰 크기와 추가 의존성으로 인해 AtomWorks와 함께 자동으로 설치되지 않으므로, 사용할 계획이 있는 경우에만 설치하세요.

2. 개발용 설치
---------------------------
개발을 위해:

.. code-block:: bash

   git clone https://github.com/RosettaCommons/atomworks.git
   cd atomworks
   make install  # 또는 pip install -e ".[dev]"

새로운 환경에 설치하려면:

.. code-block:: bash

   git clone https://github.com/RosettaCommons/atomworks.git
   cd atomworks
   make env


3. 테스트 스위트 실행
-------------------------

AtomWorks 테스트 스위트를 실행하려면 테스트 데이터를 다운로드하고 환경 변수를 구성해야 합니다.

**1단계: 테스트 데이터 다운로드**

리포지토리 루트에서 다음을 실행합니다:

.. code-block:: bash

   atomworks setup tests

이 명령은 테스트 팩(~500MB)을 다운로드하고 ``tests/data/``에 압축을 풉니다. 여기에는 다음이 포함됩니다:

* ``tests/data/pdb/`` — 테스트 구조가 포함된 미니 PDB 미러
* ``tests/data/ccd/`` — 테스트 리간드 정의가 포함된 미니 CCD 미러
* ``tests/data/shared/`` — ML 테스트를 위한 MSA 파일, 템플릿 및 메타데이터

**2단계: .env 파일 생성**

리포지토리 루트에 테스트 데이터 경로가 포함된 ``.env`` 파일을 생성합니다:

.. code-block:: bash

   # 테스트 팩으로 테스트를 실행하기 위해:
   PDB_MIRROR_PATH=tests/data/pdb
   CCD_MIRROR_PATH=tests/data/ccd

``.env.sample``을 시작점으로 복사할 수 있습니다:

.. code-block:: bash

   cp .env.sample .env
   # 그 다음 .env를 편집하여 위의 경로를 설정하세요

**3단계: 테스트 실행**

.. code-block:: bash

   # 모든 테스트 실행 (매우 느린 테스트 제외)
   pytest tests -m "not very_slow"

   # 더 빠른 실행을 위해 병렬로 테스트 실행
   pytest tests -m "not very_slow" -n auto

   # 특정 테스트 파일 실행
   pytest tests/io/components/test_parser.py


4. 전체 PDB/CCD 미러 설정
----------------------------------

프로덕션 사용 또는 전체 PDB에 대한 학습을 위해서는 테스트 서브셋이 아닌 전체 미러가 필요합니다. 다음에 대한 자세한 지침은 :doc:`mirrors_ko`를 참조하세요:

* 전체 PDB 미러 설정 (~100 GB)
* CCD 미러 설정 (~2 GB)
* 프로덕션 사용을 위한 환경 변수 구성

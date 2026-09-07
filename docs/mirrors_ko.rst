데이터 미러 (Data Mirrors)
============

AtomWorks는 구조 파싱 및 모델 학습을 위해 PDB 및 CCD 데이터베이스의 로컬 미러를 사용합니다. 이 페이지에서는 이러한 미러를 설정하는 방법을 설명합니다.

미러 설정
------------------

PDB 미러 (~100 GB)
^^^^^^^^^^^^^^^^^^^^

PDB 미러에는 mmCIF 구조 파일이 포함되어 있습니다. 권장 형식으로 mmCIF를 사용합니다.

.. code-block:: bash

   # 전체 PDB 다운로드
   atomworks pdb sync /path/to/pdb_mirror

   # 또는 특정 PDB ID만 다운로드
   atomworks pdb sync /path/to/pdb_mirror --pdb-id 1A0I --pdb-id 7XYZ

   # 또는 ID 파일에서 다운로드 (한 줄에 하나씩)
   atomworks pdb sync /path/to/pdb_mirror --pdb-ids-file /path/to/ids.txt

이렇게 하면 RCSB와 동일한 샤딩 패턴(예: ``1a2b`` → ``/path/to/pdb_mirror/a2/1a2b.cif.gz``)으로 PDB의 사본이 생성됩니다.

CCD 미러 (~2 GB)
^^^^^^^^^^^^^^^^^^

CCD(Chemical Component Dictionary) 미러에는 비폴리머 엔터티를 파싱하는 데 사용되는 리간드 정의가 포함되어 있습니다.

.. code-block:: bash

   atomworks ccd sync /path/to/ccd_mirror

CCD 미러가 제공되지 않으면 AtomWorks는 Biotite의 내부 CCD를 대체 사용합니다. CCD 패턴(예: ``/path/to/ccd_mirror/M/MYLIGAND/MYLIGAND.cif``)에 따라 미러에 CIF 파일을 배치하여 사용자 정의 리간드 정의를 추가할 수도 있습니다.

환경 변수 구성
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

미러가 생성되면 리포지토리 루트의 ``.env`` 파일에 경로를 구성합니다:

.. code-block:: bash

   PDB_MIRROR_PATH=/path/to/pdb_mirror
   CCD_MIRROR_PATH=/path/to/ccd_mirror

``.env.sample``을 시작점으로 복사할 수 있습니다. 환경 설정에 대한 자세한 내용은 :doc:`installation_ko`를 참조하세요.

----

PDB에서 학습하기
===================

1단계 — PDB 미러링 (mmCIFs)
--------------------------------

PDB에서 학습하려면 구조 파일에 액세스해야 합니다. 위의 지침에 따라 PDB 미러를 설정하세요.

2단계 — PDB 메타데이터 가져오기 (PN 단위 및 인터페이스)
---------------------------------------------------

샘플링 확률을 계산하고 분할을 위한 예제를 필터링하기 위해 각 PDB 항목에 대한 메타데이터로 PDB를 전처리합니다.
작업을 덜어드리기 위해 미리 계산된 메타데이터(2025년 7월 15일자)를 다운로드할 수 있도록 제공합니다:

.. code-block:: bash

  atomworks setup metadata /path/to/metadata  # 메타데이터(.tar.gz)를 다운로드하고 지정된 디렉터리에 압축을 풉니다.


이렇게 하면 다음 위치에 parquet 파일이 생성됩니다:

* ``/path/to/metadata/pn_units_df.parquet`` — PDB의 각 **PN unit**에 대한 메타데이터를 포함합니다. **pn unit**이라는 용어는 ``polymer XOR non-polymer unit``의 약어이며 거의 모든 목적에서 PDB 파일의 ``chain``과 같이 작동합니다. 유일한 차이점은 여러 공유 결합된 리간드로 구성된 리간드는 단일 PN 단위로 간주된다는 것입니다(PDB 파일에서는 여러 체인이 됨). 사실상 이 ``.parquet``은 필터링 및 샘플링에 유용한 메타데이터를 포함하는 PDB의 모든 개별 체인, 리간드 등의 큰 테이블입니다(정확히 말하면 pn unit당 하나의 항목이 있음).
* ``/path/to/metadata/interfaces_df.parquet`` — PDB의 각 인터페이스에 대한 메타데이터를 포함합니다. 이 ``.parquet``은 PDB의 모든 이진 인터페이스의 큰 테이블입니다. 각 인터페이스를 (pn_unit_1, pn_unit_2) 쌍으로 나열하고 필터링 및 샘플링에 유용한 메타데이터를 포함합니다.

  대안으로, 더 최신의 메타데이터를 직접 생성할 수도 있습니다(스크립트는 몇 주 내에 업로드될 예정입니다).

3단계 — AF3 스타일 데이터셋 구성 (예: D-폴리펩타이드에서만 학습)
-------------------------------------------------------------------------------

다음으로 메타데이터를 사용하여 샘플링하려는 데이터셋을 구성해야 합니다. 여기에는 학습 컷오프, 필터, 적용할 변환 등이 포함됩니다.
다음은 간단한 예입니다:

* D-폴리펩타이드 및 L-폴리펩타이드 체인으로만 필터링합니다 (`POLYPEPTIDE_D` 및 `POLYPEPTIDE_L` -- 추가 체인 유형을 포함하려면 목록을 적절한 ID로 대체하세요(주석의 [매핑](./src/atomworks/enums.py#L31-L45) 참조)).
* [`atomworks.io.constants.AF3_EXCLUDED_LIGANDS_REGEX`](./src/atomworks/constants.py#L350)에서 사용할 수 있는 AF3 제외 리간드 목록에 있는 리간드를 제외합니다.

.. code-block:: yaml

  # 참고: 아래는 hydra 구성이며 _target_ 필드는 클래스를 인스턴스화하기 위한 hydra 구문입니다.
  # hydra 없이 사용할 수 있지만, 그럴 경우 _target_ 객체에 해당하는 인수를 직접 제공해야 합니다.

   아래 사용된 Chain type id (atomworks.enums.ChainType에서):
  # 0=CyclicPseudoPeptide, 1=OtherPolymer, 2=PeptideNucleicAcid,
  # 3=DNA, 4=DNA_RNA_HYBRID, 5=POLYPEPTIDE_D, 6=POLYPEPTIDE_L, 7=RNA,
  # 8=NON_POLYMER, 9=WATER, 10=BRANCHED, 11=MACROLIDE

  af3_pdb_dataset:
    _target_: atomworks.ml.datasets.datasets.ConcatDatasetWithID
    datasets:
      # 단일 PN units
      - _target_: atomworks.ml.datasets.datasets.StructuralDatasetWrapper
        dataset_parser:
          _target_: atomworks.ml.datasets.parsers.PNUnitsDFParser
        transform:
          _target_: atomworks.ml.pipelines.af3.build_af3_transform_pipeline
          is_inference: false
          n_recycles: 5  # 이것은 각 예제에 대해 MSA에서 5개의 무작위 세트를 하위 샘플링함을 의미합니다.
          crop_size: 256
          crop_contiguous_probability: 0.3333333333333333
          crop_spatial_probability: 0.6666666666666666
          diffusion_batch_size: 32
          # 선택적 템플릿 (사용 가능한 경우)
          template_lookup_path: ${paths.shared}/template_lookup.csv
          template_base_dir: ${paths.shared}/template
          # 선택적 MSA (4단계 참조)
          # protein_msa_dirs:
          #   - { dir: /path/to/msa, extension: .a3m.gz, directory_depth: 2 }
          # rna_msa_dirs:
          #   - { dir: /path/to/msa, extension: .afa, directory_depth: 0 }
        dataset:
          _target_: atomworks.ml.datasets.datasets.PandasDataset
          name: pn_units
          id_column: example_id
          data: /path/to/metadata/pn_units_df.parquet
          filters:
            - "deposition_date < '2022-01-01'"
            - "resolution < 5.0 and ~method.str.contains('NMR')"
            - "num_polymer_pn_units <= 20"
            - "cluster.notnull()"
            - "method in ['X-RAY_DIFFRACTION', 'ELECTRON_MICROSCOPY']"
            # D-폴리펩타이드에서만 학습:
            - "q_pn_unit_type in [5, 6]"  # 5 = POLYPEPTIDE_D, 6 = POLYPEPTIDE_L
            # AF3 제외 세트에서 리간드 제외:
            - "~(q_pn_unit_non_polymer_res_names.notnull() and q_pn_unit_non_polymer_res_names.str.contains('${af3_excluded_ligands_regex}', regex=True))"
          columns_to_load: null
        save_failed_examples_to_dir: null

      # 이진 인터페이스 (Binary interfaces)
      - _target_: atomworks.ml.datasets.datasets.StructuralDatasetWrapper
        dataset_parser:
          _target_: atomworks.ml.datasets.parsers.InterfacesDFParser
        transform:
          _target_: atomworks.ml.pipelines.af3.build_af3_transform_pipeline
          is_inference: false
          n_recycles: 5
          crop_size: 256
          crop_spatial_probability: 1.0
          crop_contiguous_probability: 0.0
          diffusion_batch_size: 32
          template_lookup_path: ${paths.shared}/template_lookup.csv
          template_base_dir: ${paths.shared}/template
          # 선택적 MSA (4단계 참조)
          # protein_msa_dirs:
          #   - { dir: /path/to/msa, extension: .a3m.gz, directory_depth: 2 }
          # rna_msa_dirs:
          #   - { dir: /path/to/msa, extension: .afa, directory_depth: 0 }
        dataset:
          _target_: atomworks.ml.datasets.datasets.PandasDataset
          name: interfaces
          id_column: example_id
          data: /path/to/metadata/interfaces_df.parquet
          filters:
            - "deposition_date < '2022-01-01'"
            - "resolution < 5.0 and ~method.str.contains('NMR')"
            - "num_polymer_pn_units <= 20"
            - "cluster.notnull()"
            - "method in ['X-RAY_DIFFRACTION', 'ELECTRON_MICROSCOPY']"
            # D-폴리펩타이드 인터페이스에서만 학습:
            - "pn_unit_1_type in [5, 6]"  # 5 = POLYPEPTIDE_D, 6 = POLYPEPTIDE_L
            - "pn_unit_2_type in [5, 6]"  # 5 = POLYPEPTIDE_D, 6 = POLYPEPTIDE_L
            - "~(pn_unit_1_non_polymer_res_names.notnull() and pn_unit_1_non_polymer_res_names.str.contains('${af3_excluded_ligands_regex}', regex=True))"
            - "~(pn_unit_2_non_polymer_res_names.notnull() and pn_unit_2_non_polymer_res_names.str.contains('${af3_excluded_ligands_regex}', regex=True))"
          columns_to_load: null
        cif_parser_args:
          cache_dir: null
        save_failed_examples_to_dir: null

4단계 — 모델 학습
----------------------

이제 모델을 학습시키는 데 사용할 수 있는 본격적인 데이터셋이 준비되었습니다! 전체 PDB와 메타데이터를 다운로드할 필요 없이 그냥 시도해보고 싶다면, 실제 PDB 파일, 메타데이터, 증류(distillation) 데이터, 템플릿 및 AF3 예제를 위한 MSA가 포함된 미니 목업 파이프라인이 있는 테스트를 대신 실행할 수 있습니다. atomworks CLI를 통해 관련 메타데이터를 모두 다운로드할 수 있습니다:

.. note::

  다음 명령을 실행할 때 AtomWorks 루트 디렉터리에 있는지 확인하세요. 그렇지 않으면 현재 작업 디렉터리에 새 tests/data 폴더가 생성됩니다.

.. code-block:: bash
  atomworks setup tests  # 이렇게 하면 테스트 팩이 `tests/data`에 다운로드되고 압축이 풀립니다 (~500MB).

이제 `tests/data/pdb`에 미니 PDB가 있고 `tests/data/ccd`에 미니 사용자 정의 CCD가 있습니다. 증류 및 메타데이터는 `data/ml/af2_distillation`, `data/ml/pdb_pn_units` 및 `data/ml/pdb_interfaces`에 있습니다. 이 모든 것을 사용하는 데이터셋은 [예: 여기](./tests/ml/conftest.py#L300)에 있습니다.

다양한 데이터셋에 대한 테스트를 실행하려면 다음 명령을 실행할 수 있습니다:

.. code-block:: bash

  # 올바른 환경이 활성화되어 있는지 확인하고 .env 파일 / 쉘 환경 변수에서 경로를 올바르게 설정하세요 (위의 사항 참조)
  pytest tests/ml/pipelines/test_data_loading_pipelines.py

.. _contributor-best-practices:

===============================
기여하기
===============================

.. note::
   이것은 업계 표준과 우리 팀의 경험을 바탕으로 코드를 기여하기 위한 모범 사례의 일부 목록입니다.

코딩할 때
-------------

1. **인지 부하 줄이기:**

   a. 의미 있고 설명적인 변수 이름을 선택하세요.

   b. 독스트링(AI 활용!)과 주석을 작성하세요. API 문서에 사용되려면 독스트링은
      Google 스타일 가이드를 따라야 합니다: `Google Python Style Guide <https://google.github.io/styleguide/pyguide.html#38-comments-and-docstrings>`_.

   c. `Python Zen <https://peps.python.org/pep-0020/>`_을 따르세요 – 명시적인 것이 암시적인 것보다 낫습니다 등.

2. **테스트를 작성하세요.**

커밋할 때
---------------

1. 커밋을 "하나의 논리적 단위"로 유지하세요. 즉, 각 커밋은 하나의 작업을 완료하거나, 하나의 버그를 수정하거나, 하나의 기능을 구현하는 관련된 변경 사항의 집합이어야 합니다.
   `VS Code <https://code.visualstudio.com/docs/sourcecontrol/overview>`_와 같은 편집기를 사용하거나
   `GitHub Desktop <https://docs.github.com/en/desktop>`_을 사용하면 관련된 변경 사항을 함께 스테이징하는 데 도움이 될 수 있습니다.

2. `시맨틱 커밋 규칙(semantic commit conventions) <https://www.conventionalcommits.org/en/v1.0.0/>`_을 준수하세요.

3. 코드를 포맷팅하고 린트하세요 (``make format``).

4. 사람들이 당신이 이 작업을 하고 있다는 것을 알고 초기에 조언/피드백을 제공할 수 있도록 초안(draft) PR을 제출하세요.

PR을 마무리할 때
---------------------

1. PR을 만들기 위해 브랜치를 **staging**에 병합하세요. 관리자는 정기적으로 staging을 production에 병합합니다.
2. 전체 PR을 400 LOC(코드 라인 수) 미만으로 유지하세요 (경험 법칙: 500 LOC는 검토하는 데 약 1시간이 걸립니다).
3. `PR 체크리스트 <https://github.com/RosettaCommons/atomworks/blob/production/.github/pull_request_template.md>`_를 읽고 작성하세요.

검토할 때
---------------

1. 긍정적인 검토 문화를 조성하세요 – 우리는 서로에게서 배우기를 원합니다. 비판적이되 친절하세요.
2. 가벼운 코드 리뷰를 연습하세요. 버그를 수정하거나 / 문서를 개선하거나 / 아주 작은 기능을 추가하는 작은 것을 atomworks.io/atomworks.ml에 제출하여 24시간 이내에 연습해 보세요. (30분 미만 소요 가능)
3. 집중을 위해 검토 시간을 1시간 미만, 500 LOC 미만으로 유지하세요.

문서에 기여하기
---------------------------------
외부 AtomWorks 문서는 `Sphinx <https://www.sphinx-doc.org/en/master/#>`_를 사용하여 빌드되고 `GitHub Pages <https://docs.github.com/en/pages>`_에서 호스팅됩니다.
AtomWorks 및 해당 의존성이 설치되어 있는 것 외에도, 로컬에서 문서를 빌드하려면 문서 요구 사항을 설치해야 합니다:

.. code-block:: bash

   uv pip install -r docs/docs_requirements.txt

문서를 빌드하려면 ``docs`` 디렉터리로 이동하여 다음을 실행하세요:

   .. code-block:: bash

      make html

Sphinx가 처음이라면 문서 작성 및 포맷팅에 대한 지침은 `Sphinx 문서 <https://www.sphinx-doc.org/en/master/>`_를 참조하세요.
모든 문서는 reStructuredText (reST) 형식으로 작성되었습니다. reST에 대한 자세한 내용은 `reStructuredText 입문 <https://docutils.sourceforge.io/docs/user/rst/quickstart.html>`_을 참조하세요.

기타 리소스
---------------

- `코드 리뷰 모범 사례 | SmartBear <https://smartbear.com/learn/code-review/best-practices-for-peer-code-review/>`_


.. raw:: html

   <hr>

PR 위생 (PR Hygiene)
=================

이 리포지토리에 기여할 때는 다음 단계를 따르세요:

1. 리포지토리 복제
2. 개발 환경 생성 (설치 가이드의 *로컬 Conda 환경* 섹션 참조).
3. 변경 사항을 위한 새 브랜치 생성.
   - 브랜치 이름은 다음 규칙을 사용하세요: ``<category>/<description>``. 카테고리: ``feat``, ``fix``, ``hotfix``, ``refactor``, ``docs``, ``perf``.
   - 예: ``feat/support-rdkit-small-molecule``
4. 새 브랜치에서 변경 사항을 만들고 커밋하세요.
   - 커밋하기 전에 자동 포맷팅 도구(``make format``)를 실행하세요.
   - ``<type>: <description>``과 같은 커밋 메시지를 사용하세요. 유형: ``feat``, ``fix``, ``refactor``, ``docs``, ``chore``, ``wip``.
   - 예: ``git commit -m "docs: add contributing guidelines"``
5. ``staging``에 풀 리퀘스트를 열고 변경 사항을 설명하세요.
6. 검토를 기다리고 변경 사항을 병합하세요.

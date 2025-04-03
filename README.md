# reposcore-py
A CLI for scoring student participation in an open-source class repo, implemented in Python.

## Install dependencies

```bash
pip install -r requirements.txt
```

## Usage
아래는 `python reposcore -h` 또는 `python reposcore --help` 실행 결과를 붙여넣은 것이므로
명령줄 관련 코드가 변경되면 아래 내용도 그에 맞게 수정해야 함.

```
usage: __main__.py [-h] --repo REPO [--output OUTPUT] [--format {table,chart,both}]

A CLI tool to score participation in an open-source course repository

options:
  -h, --help            show this help message and exit
  --repo REPO           Path to the git repository
  --output OUTPUT       Output directory for results
  --format {table,chart,both}
                        Output format
```

## Score Formula
아래는 PR 개수와 이슈 개수의 비율에 따라 점수로 인정가능한 최대 개수를 구하고 각 배점에 따라 최종 점수를 산출하는 공식이다.

- $P_f$ : 기능 관련 Merged PR 개수 (**3점**)  
- $P_b$ : 버그 관련 Merged PR 개수 (**3점**)  
- $P_d$ : 문서 관련 Merged PR 개수 (**2점**) 
- $I_f$ : 기능 관련 Open 또는 해결된 이슈 개수 (**2점**)  
- $I_b$ : 버그 관련 Open 또는 해결된 이슈 개수 (**2점**)  
- $I_d$ : 문서 관련 Open 또는 해결된 이슈 개수 (**1점**)

점수로 인정 가능한 PR의 개수\
$P_{\text{valid}} = P_f + P_b + \min(P_d, 3(P_f + P_b))$

점수로 인정 가능한 이슈의 개수\
$I_{\text{valid}} = \min(I_f + I_b + I_d, 4 \times P_{\text{valid}})$

PR의 점수를 최대로 하기 위해 기능/버그 PR을 먼저 계산한 후 문서 PR을 계산합니다.

기능/버그 PR을 최대로 포함:\
$P_f^* + P_b^* = \min(P_f + P_b, P_{valid})$

남은 개수에서 문서 PR을 포함:\
$P_d^* = P_{valid} - (P_f^* + P_b^*)$

이슈의 점수를 최대로 하기 위해 기능/버그 이슈를 먼저 계산한 후 문서 이슈를 계산합니다.

기능/버그 이슈를 최대로 포함:\
$I_f^* + I_b^* = \min(I_f + I_b, I_{valid})$

남은 공간에서 문서 이슈를 포함:\
$I_d^* = I_{valid} - (I_f^* + I_b^*)$

최종 점수 계산 공식:\
$`S = 3(P_f^* + P_b^*) + 2P_d^* + 2(I_f^* + I_b^*) + 1I_d^*`$
v2 재측정 최종 결과
901 질의 / 1,237 에피소드 프로토콜. 모든 수치는 153질의(reader) 또는 900질의(classifier) 공통 질의 부분집합, paired bootstrap 10,000회(seed 20261030).

1. 배포 최종 조합 vs 단일 baseline (가장 중요한 표)
방법	Top-1 (%)	차이	95% CI	p
deploy_final (예측특징 + block prior 0.75 + BM25 tie-break)	46.4	—	—	—
BM25 + block prior (λ=CV)	44.6	+1.9	[-1.2, +5.1]	0.2532
한계 명시: 최종 조합의 우위는 기존 배포 경로 대비이고, block prior를 똑같이 붙인 BM25와는 통계적으로 구분되지 않습니다. "컴파일이 텍스트 검색을 이긴다"로 쓰면 안 됩니다.

2. I3: 동일 reader를 3개 후보 리스트에 적용 (153 질의)
arm	후보 GT@10	rc-class@10	GT@1 base → a → b	GT@3 base → a	GT@5 base → a
layer1 cascade	88.2	86.3	32.0 → 40.5 → 39.2	66.7 → 66.0	79.7 → 76.5
block-only	84.3	83.7	51.6 → 38.6 → 37.3	68.0 → 75.2	76.5 → 81.7
BM25	68.0	92.2	32.0 → 34.6 → 32.7	49.0 → 52.3	58.8 → 58.2
(a) = signature만, (b) = signature + symptom page.

paired test (a vs base)	차이	95% CI	p
layer1 GT@1	+8.5	[-0.7, +17.6]	0.0808
bm25 GT@1	+2.6	[-3.9, +9.2]	0.5086
block GT@1	-13.1	[-23.5, -2.6]	0.0182
block GT@3	+7.2	[+0.7, +13.7]	0.0404
block GT@5	+5.2	[+0.7, +9.8]	0.0264
layer1 rc@1	+11.1	[+2.6, +20.3]	0.0156
page (b vs a) GT@1, 3개 리스트	-1.3 / -2.0 / -1.3	모두 0 포함	>0.5
해석: reader는 "상위 5 안에 넣는" 일을 잘하고(+7.2, +5.2, 유의), "1등을 뒤집는" 판단은 base 순서가 좋을 때만 손해입니다(block -13.1, 유의). 유일한 유의한 개선은 원인이 아니라 원인 클래스에서 나옵니다(rc@1 30.7 → 41.8). symptom page는 top-1 기준 어느 리스트에서도 기여하지 않습니다.

3. I4: 분류기 재분류 — 라벨 정확도는 병목이 아니다
예측 세트	type@1	type@3	rc@1	parse 실패	동일 ranker Top-1	MRR
e4_multi (claude)	52.6	78.4	42.2	1.1	43.1	0.5921
e4_multi_cocoa (모델 대조)	56.8	82.6	39.8	2.2	44.1	0.5965
e4_rag (retrieved context + 모델)	73.9	94.3	60.4	3.0	44.0	0.5977
paired (Top-1)	차이	95% CI	p
rag vs multi	+0.9	[-1.7, +3.4]	0.5144
multi_cocoa vs multi (모델 효과)	+1.0	[-1.2, +3.2]	0.4008
rag vs multi_cocoa (문맥 효과)	-0.1	[-2.3, +2.1]	0.9492
rag vs layer1_predicted_block	+5.1	[+1.6, +8.8]	0.0042
rag vs combo_predicted_rc	+0.1	[-2.7, +3.0]	0.9724
top-3 union vs top-1 (rag)	-1.8	[-4.4, +0.9]	0.2072
귀인: 라벨 +17.1(points) / rc +20.6 상승에도 Top-1은 44.1 → 44.0. 모델 효과 +1.0(p=0.40), RAG 문맥 효과 -0.1(p=0.95). Top-1 type이 64.3%만 일치하는 두 예측 세트가 동일하게 검색합니다. 병목은 라벨 품질이 아니라 ordering rule과 prior입니다.

4. 백엔드 대조 (모든 reader 비교의 전제)
동일 프롬프트, 149 질의, 두 백엔드: 순서 완전 일치 0.0%. base에서 top-1이 이동한 비율 COCOA 75.8% / 71.8%(a/b), claude 72.5% / 67.8%. 따라서 reader가 들어간 비교는 반드시 단일 백엔드로만 해석해야 합니다.

5. 문서 반영 내역
doc/results_v2_summary_kor.md: §2.4(1)에 BM25+prior 44.6 행과 한계 bullet 추가, §2.4(4)를 "LLM 차단으로 미실행" → 실측 3부(백엔드 민감도 / I3 / I4)로 전면 교체, §4-13·§4-15 갱신.
doc/paper_dvcon_v2.tex: 초록 2문장 추가, §hybrid에 분류기 대조 문단, RQ3에 153질의 확장 문단 추가. 구조 검사(brace 균형, ASCII, $ 개수) 통과.
6. 정정 사항
이전 중간보고에서 "GT@3·GT@5는 모든 arm에서 개선 없음"이라 적었는데 사실이 아닙니다. block 리스트는 깊이에서 유의하게 개선됩니다(+7.2 p=0.040, +5.2 p=0.026). 위 표가 정정된 값입니다.



DVCon 2027 논문 강화 5항목 삽입 완료
측정은 끝났는데 논문에 없던 분석 5개를 doc/paper_dvcon_v2.tex에 삽입했습니다. 기존 주장을 약화시키지 않고, 논문이 이미 인정한 한계를 정면으로 다루는 증거만 넣었습니다. 525줄 → 541줄, 구조 검사(brace 0, ASCII, $ 짝, begin/end 25/25) 통과.

삽입 위치와 근거 수치
#	항목	위치	핵심 수치
1	GT 순환성 반증	Threats, Ground-truth circularity 말미 + 신규 절	명시적 참조 GT(51): 35.3 → 49.0 (+13.7 [+2.0,+27.5] p=0.049), 같은 변경의 블록 GT 이득은 +4.1
2	교차 블록 메커니즘 채널	신규 절 What the block-based label cannot see	재발쌍 52(교차 22): 기존 배포 13.5%, mechanism 단독 40.4%. mu=1.0에서 블록 GT -2.7(p=0.09) / 명시적 GT +15.7(p=0.007)
3	gold feature 상한	Classifying unresolved failures 말미	43.1 → 44.9 (+1.8). gold type만 +1.7, gold 키워드만 +0.6
4	식별자 누설 반증	Threats, 신규 Identifier leakage	900질의 43.1 → 43.2, 변경 2건, 순효과 +1 hit
5	rc 게이트 정당화	Classifying unresolved failures 말미	무게이트 45.3이지만 Top-3 70.3→65.4. count gate 43.9 / 70.3 / 82.3 / 92.3. rc 맞음 47.9→54.2, 틀림 39.6→36.3
왜 이 5개인가
1·2가 가장 중요합니다. 논문은 Threats에서 "block GT는 순환적"이라 자인하면서 대응 지표가 없었습니다. 이제 (a) 특정 에피소드를 채점하는 51질의, (b) 블록과 무관한 129 재발쌍으로 측정해 "블록 GT에서의 하락은 채널의 손실이 아니라 지표의 사각지대"를 표로 증명합니다.
3은 연구 방향을 닫습니다. 완벽한 라벨을 공짜로 줘도 +1.8점. 최근 넣은 I4(라벨 +17.1 → Top-1 -0.1)의 상한을 900질의 전수로 못박습니다.
4·5는 리뷰어 질문 선제 차단입니다. "이슈 번호를 외운 것 아니냐"(2줄로 반증), "왜 게이트가 필요한가"(무게이트가 깊이를 4.9pp 깎는다는 실측).
삽입된 영문 원문
[3] gold feature 상한

That last statement is measured on a cascade whose within-bucket order is the episode id, and it is not the ceiling of the classifier line on the ranker we deploy. We therefore decompose the deployed ranker's own error over the 900 predicted-feature queries, replacing one input at a time with the value compiled from the resolved episode: predicted type with predicted keywords gives 43.1% Top-1, gold type with predicted keywords 44.8% (+1.7), predicted type with gold keywords 43.7% (+0.6), and both gold 44.9% (+1.8). An oracle classifier that named every type and every keyword exactly as the resolved episode does would move the deployed Top-1 by 1.8 points. The two estimates are consistent rather than contradictory, and the difference between them locates the classifier's cost precisely: on the id-ordered cascade a correct type changes which blockxtype bucket is cut, so the 4.8-point gap of the paragraph above is a bucket-coverage effect; under the deployed rule the bucket cut is unchanged and the BM25 tie-break decides the order inside it, so the same information is worth only 1.8 points. Classification is worth improving for coverage, for routing, and because its errors are systematic rather than random, but on this ranker it is not the binding constraint: even a perfect label leaves Top-1 at 44.9%.
[5] rc 게이트 정당화

The root-cause class is the only predicted label that can enter the score directly, because the type is spent on the bucket key. We tested it as a routing term - every candidate in the query's block whose compiled class equals the predicted class receives +\rho - and the term needs a gate to be safe. Ungated at \rho=0.05 it raises Top-1 to 45.3% but costs 4.9 points at Top-3 (70.3% to 65.4%), 5.4 at Top-5 and 4.6 at Top-10: it promotes same-class candidates past everything else and empties the deeper positions. The gate the cross-fit selects instead activates the term only when at most theta candidates in the query's block carry the predicted class (the two folds choose \theta=3 and \theta=1, \rho=0.05 in both), and that configuration is loss-free: out-of-fold Top-1 is 43.9% with Top-3 70.3%, Top-5 82.3% and Top-10 92.3%, all at or above the ungated ranker's 43.1% / 70.3% / 81.6% / 92.2%. The distribution explains why the gate is needed: 97 of the 900 queries (10.8%) have no visible in-block episode of the predicted class, and 331 (36.8%) have three or more, so an ungated term is either inert or dominant depending on the query. The split by label correctness bounds what the term can ever buy: where the predicted class is right (380 queries) Top-1 moves from 47.9% to 54.2%, and where it is wrong (520 queries) from 39.6% to 36.3%. The term is a prior on a label with 42.2% accuracy, so it pays only in the cells where the block already supplies the candidates and the label is trustworthy, which is what the count gate measures.
[1][2] 신규 절: What the block-based label cannot see

== What the block-based label cannot see ==Every number reported so far is scored by a ground truth that accepts any episode from the same block with the same root cause, so a ranker that returns a cross-block recurrence is charged with a miss even when it returns the episode the engineer actually meant. The corpus offers two ways to measure that blind spot. The first is a subset of 51 queries whose ground truth names a specific episode, an explicit JIRA reference, rather than accepting a block-and-class match; these are the only queries in the set where a particular episode is the right answer. On that subset the shipped predicted-feature cascade, which reaches 39.0% over the 901, reaches 35.3% Top-1 (Top-3 64.7%, Top-5 74.5%). Replacing the id tie-break with the within-bucket BM25 re-rank of Section Sec., the same change that is worth +4.1 points over the whole set [+0.6,+7.7], lifts the subset from 35.3% to 49.0% (+13.7, [+2.0,+27.5], p=0.049), although its Top-5 falls from 74.5% to 70.6%. The two labels agree on the direction and disagree on the size: a change that is modest under the block label is large when a specific episode is scored, which is the signature of a metric that is not counting the thing the user wants.The second measurement uses the 129 explicitly cited recurrence pairs, where one issue names another as the same failure. Fifty-eight are chronologically usable, and 52 have both sides compiled and predicted: 30 within a block and 22 across blocks. We score whether the referenced episode is retrieved at all, a question the block label cannot ask. A mechanism channel scores the query's pre-resolution text against the compiled mechanism line of each candidate - type, keywords and gist, with no block input - by BM25, normalised within the horizon-safe candidate set. Alone it reaches 40.4% hit@1 on the 52 pairs, against 13.5% for the shipped deployment and 3.8% for the block-only ordering. Cross-block recurrence is therefore retrievable from compiled content, and the block-scored rows of this paper were not measuring it.Adding the channel to the cascade as one more term with weight mu traces the trade-off, and the two metrics move in opposite directions at every weight. At \mu=0 the ranker scores 43.2% on the 900 block-scored queries, 49.0% on the 51 explicit-reference queries, 26.9% on the pairs and 0.0% on the 22 cross-block pairs. At \mu=1.0 the block-scored set falls 2.7 points [-5.8,+0.3], p=0.09, while the explicit-reference set rises to 64.7% (+15.7, [+5.9,+27.5], p=0.007), pair hit@1 rises to 34.6% and cross-block pair hit@1 to 18.2%. At \mu=3.0 the pairs reach 46.2% and the cross-block pairs 59.1%, with cross-block hit@3 up +59.1 points [+40.9,+77.3], but the block-scored set falls 13.3 points [-16.9,-9.8]. The loss that the block metric charges is the gain that the reference metric records, which is what a metric blind spot looks like when it is measured rather than asserted. We keep \mu=0 in the deployed configuration, because the block-scored set is 900 queries and the explicit-reference set is 51, and we do not tune the channel on either; it is reported as the size of what the deployed metric does not count. Two caveats bound the reading. The operating point was chosen after this table was produced, so its p-value describes the trade-off curve and is not a confirmatory test, and cross-fitting on pair hit@1 would select 1.5 and 3.0, a rule fitted to the pair metric rather than a deployment setting. The channel is also close to a keyword match on a compiled summary line, so part of its advantage may be vocabulary that the hash cascade deliberately discards.
[4] Identifier leakage

Identifier leakage. The compiled features are extracted from tickets that name other tickets, so a cascade could in principle retrieve by memorised identifier rather than by failure content. Removing every JIRA key and every number of four or more digits from the pre-resolution text and re-running the full pipeline moves Top-1 from 43.1% to 43.2%, changes the answer of 2 of the 900 queries, and has a net effect of one hit; on the 52 recurrence pairs the mechanism channel's weight sweep is identical at every \mu\ge 0.2 under raw identifiers, scrubbed text and scrubbed keywords. Identifier matching contributes nothing measurable to any number in this paper. The check bounds identifier matching only: it says nothing about domain vocabulary that both sides of a match share, which is the correspondence the method is supposed to exploit.
한계
이 노드에 LaTeX이 없어 PDF 빌드는 미검증입니다. brace/ASCII/$ 짝/환경 짝만 기계 검사했습니다.
mu=1.0 운용점은 표를 본 뒤 고른 사후 값이라 p값은 확증 검정이 아니라는 caveat을 본문에 명시했습니다.
최종 조합 46.4(6번 항목)는 넣지 않았습니다. 논문 헤드라인 43.7과 프로토콜이 달라 별도 정합 작업이 필요합니다.

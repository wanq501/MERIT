<h1> 
  <p align=center> MERIT: Mutation-Exposed Weakness Ranking for Iterative Testing With Open-Source Code Large Language Models </p>
<div align="center">

![Python 3.10](https://img.shields.io/badge/python-3.10-g)
![vLLM](https://img.shields.io/badge/serving-vLLM-blue.svg)
![Java 11](https://img.shields.io/badge/java-11-orange.svg)
![PIT 1.15.3](https://img.shields.io/badge/PIT-1.15.3-red.svg)
[![docs](https://img.shields.io/badge/docs-latest-blue)](README.md)

</div>
</h1>
<img src="Assets/fig2.png" width="1500">

MERIT generates JUnit 4 test suites with a locally deployable 30B-class open-source code LLM. Every candidate test passes a compile-and-execute (C/E) gate before any measurement, PIT and JaCoCo expose what the verified suite still misses, and a mutation-first ranking policy decides which weaknesses reach the next prompt. A protected set and a per-round best snapshot make the iteration non-regressive at the suite level, so the delivered suite is never worse than any suite the loop has already measured.

- **Runs on one GPU.** No external API, no 70B-scale serving, so the code never leaves the premises.
- **Valid by construction.** The C/E gate repairs or discards every test that fails against the class under test.
- **Multi-weakness feedback.** Surviving mutants, unreached mutants, and coverage gaps enter one ranked prompt.
- **Non-regressive loop.** Two safeguards guarantee the final suite carries the best score measured in any round.

## Benchmarks  
<table>
  <thead align="center">
    <tr>
      <th>Benchmark</th>
      <th>Description</th>
      <th>Subjects</th>
      <th>Mutants</th>
      <th>Mutants / Subject</th>
      <th>BaiduYun Download</th>
      <th>Google Download</th>
    </tr>
  </thead>
  <tbody align="center">
    <tr>
      <td><a href="https://github.com/lin-tan/clm">HumanEval-Java</a></td>
      <td align="left">Method-level Java subjects taken verbatim from the official index of the program-repair dataset. Each subject is a self-contained algorithmic class.</td>
      <td>104</td>
      <td>1,114</td>
      <td>10.7</td>
      <td>---</td>
      <td>---</td>
    </tr>
    <tr>
      <td>LeetCode-Java</td>
      <td align="left">Rebuilt from a public corpus of LeetCode solutions under the MUTGEN protocol: 50 Medium and 50 Hard problems drawn with a fixed seed, self-contained classes only, each verified to compile in isolation.</td>
      <td>100</td>
      <td>1,829</td>
      <td>18.3</td>
      <td>---</td>
      <td>---</td>
    </tr>
  </tbody>
</table>

- Mutants are seeded by PIT 1.15.3 under its `DEFAULTS` operator group.
- A contamination-controlled split of 50 seeded subjects per benchmark, together with an $\alpha$-renamed counterpart of every subject, is released alongside the originals.

## Results  

<table>
  <thead align="center">
    <tr>
      <th>Backbone</th>
      <th>Benchmark</th>
      <th>Approach</th>
      <th>Succ.</th>
      <th>Exec</th>
      <th>Line</th>
      <th>Branch</th>
      <th>$MS$</th>
      <th>Tests</th>
      <th>Time(s)</th>
      <th>Tokens</th>
    </tr>
  </thead>
  <tbody align="center">
    <tr>
      <td rowspan="4">Qwen2.5-Coder-32B-Instruct</td>
      <td rowspan="2">HumanEval-Java</td>
      <td>Vanilla</td>
      <td>91.3</td><td>38.5</td><td>81.3</td><td>89.7</td><td>83.4</td><td>7.3</td><td>34.1</td><td>1,204</td>
    </tr>
    <tr>
      <td><b>MERIT</b></td>
      <td><b>95.2</b></td><td><b>95.2</b></td><td><b>85.0</b></td><td><b>93.2</b></td><td><b>88.8</b></td><td>11.1</td><td>105.9</td><td>3,698</td>
    </tr>
    <tr>
      <td rowspan="2">LeetCode-Java</td>
      <td>Vanilla</td>
      <td>88.0</td><td>10.0</td><td>82.4</td><td>79.2</td><td>68.1</td><td>5.2</td><td>66.9</td><td>1,092</td>
    </tr>
    <tr>
      <td><b>MERIT</b></td>
      <td><b>99.0</b></td><td><b>99.0</b></td><td><b>93.9</b></td><td><b>90.2</b></td><td><b>84.3</b></td><td>11.2</td><td>196.8</td><td>6,036</td>
    </tr>
    <tr>
      <td rowspan="4">DeepSeek-Coder-33B-Instruct</td>
      <td rowspan="2">HumanEval-Java</td>
      <td>Vanilla</td>
      <td>76.9</td><td>57.7</td><td>66.4</td><td>72.2</td><td>65.8</td><td>2.5</td><td>35.1</td><td>1,084</td>
    </tr>
    <tr>
      <td><b>MERIT</b></td>
      <td><b>77.9</b></td><td><b>77.9</b></td><td><b>68.0</b></td><td><b>74.1</b></td><td><b>69.7</b></td><td>5.6</td><td>108.1</td><td>3,616</td>
    </tr>
    <tr>
      <td rowspan="2">LeetCode-Java</td>
      <td>Vanilla</td>
      <td>38.0</td><td>20.0</td><td>34.8</td><td>32.8</td><td>27.3</td><td>0.6</td><td>42.0</td><td>952</td>
    </tr>
    <tr>
      <td><b>MERIT</b></td>
      <td><b>43.0</b></td><td><b>43.0</b></td><td><b>40.2</b></td><td><b>38.2</b></td><td><b>34.3</b></td><td>2.3</td><td>122.1</td><td>3,784</td>
    </tr>
    <tr>
      <td rowspan="4">CodeLlama-34B-Instruct</td>
      <td rowspan="2">HumanEval-Java</td>
      <td>Vanilla</td>
      <td>35.6</td><td>25.0</td><td>30.6</td><td>32.8</td><td>31.3</td><td>0.9</td><td>35.1</td><td>1,411</td>
    </tr>
    <tr>
      <td><b>MERIT</b></td>
      <td><b>38.5</b></td><td><b>38.5</b></td><td><b>32.0</b></td><td><b>34.7</b></td><td><b>33.8</b></td><td>2.1</td><td>91.5</td><td>4,337</td>
    </tr>
    <tr>
      <td rowspan="2">LeetCode-Java</td>
      <td>Vanilla</td>
      <td>27.0</td><td>3.0</td><td>22.2</td><td>20.1</td><td>13.2</td><td>0.8</td><td>62.9</td><td>1,580</td>
    </tr>
    <tr>
      <td><b>MERIT</b></td>
      <td><b>37.0</b></td><td><b>37.0</b></td><td><b>31.8</b></td><td><b>30.0</b></td><td><b>23.0</b></td><td>2.2</td><td>156.2</td><td>5,798</td>
    </tr>
  </tbody>
</table>

Search-based baselines under matched per-subject budgets (Qwen backbone, mutation score in %):

<table>
  <thead align="center">
    <tr>
      <th>Approach</th>
      <th>HumanEval-Java</th>
      <th>LeetCode-Java</th>
    </tr>
  </thead>
  <tbody align="center">
    <tr><td>EvoSuite (DynaMOSA)</td><td>64.4</td><td>66.1</td></tr>
    <tr><td>EvoSuite<sub>mut</sub> (strong mutation)</td><td>62.8</td><td>70.6</td></tr>
    <tr><td><b>MERIT</b></td><td><b>88.8</b></td><td><b>84.3</b></td></tr>
  </tbody>
</table>

- $MS$ is the PIT mutation score over all seeded mutants, averaged per subject, with a failed generation scored zero.
- Every EvoSuite variant receives a per-subject search budget equal to the mean wall-clock time of MERIT on the same benchmark, 106 s on HumanEval-Java and 197 s on LeetCode-Java.
- All models are served locally at temperature 0 with at most 1400 new tokens per call, on a single 96 GB GPU.

## Dependencies and Installation 

1. Clone and enter the repo.

   ```shell
   git clone https://github.com/wanq501/MERIT.git
   cd MERIT
   ```

2. Install Python dependencies.

   ```shell
   pip install -e .
   ```

3. Prepare the Java toolchain. JDK 11 and Maven must be on `PATH`, matching the EvoSuite runtime.

   ```shell
   java -version   # expects 11
   mvn -version
   ```

   JUnit 4.13.2, JaCoCo 0.8.11, PIT 1.15.3, and EvoSuite 1.2.0 are pinned in the build files and resolved on the first run.

4. Serve a backbone locally with vLLM. No request leaves the premises.

   ```shell
   vllm serve Qwen/Qwen2.5-Coder-32B-Instruct --port 8000
   ```

## Running MERIT 

1. Smoke test. Always validate on a small sample in an isolated output directory before launching a full run.

   ```shell
   python tools/run_matrix.py --smoke
   ```

2. Full run.

   ```shell
   python tools/run_matrix.py --benchmark humaneval_java --backbone qwen
   ```

3. Baselines.

   ```shell
   python tools/run_baselines.py --tool evosuite --budget matched
   python tools/run_baselines.py --tool vanilla
   ```

4. Ablation and contamination study.

   ```shell
   python tools/run_ablation.py          # Vanilla, +C/E, +C/E+Cov, +C/E+Mut, full
   python tools/run_perturbation.py      # alpha-renamed counterparts
   ```

5. Paper tables and figures. Every table in the paper is produced by this script alone, directly from the run records.

   ```shell
   python tools/generate_paper_results.py
   python figures/make_all.py
   ```

- Note: Each script includes detailed instructions on how to set parameters and use the script properly.
- The loop defaults to at most $R=4$ weakness rounds, a budget of $k=8$ weakness items, and at most six new tests per round.

## Code  
The code will be made public shortly after the acceptance of the paper.

## Citation

If you find our repo useful for your research, please cite us:

```


```

This project builds on the following open-source tooling.

```
@inproceedings{PIT,
  title={PIT: a practical mutation testing tool for Java},
  author={Henry Coles and Thomas Laurent and Christopher Henard and Mike Papadakis and Anthony Ventresque},
  booktitle={Proceedings of the 25th International Symposium on Software Testing and Analysis},
  pages={449--452},
  year={2016}
}
```

```
@inproceedings{EvoSuite,
  title={EvoSuite: automatic test suite generation for object-oriented software},
  author={Gordon Fraser and Andrea Arcuri},
  booktitle={Proceedings of the 19th ACM SIGSOFT Symposium and the 13th European Conference on Foundations of Software Engineering},
  pages={416--419},
  year={2011}
}
```

```
@inproceedings{vLLM,
  title={Efficient Memory Management for Large Language Model Serving with PagedAttention},
  author={Woosuk Kwon and Zhuohan Li and Siyuan Zhuang and Ying Sheng and Lianmin Zheng and Cody Hao Yu and Joseph E. Gonzalez and Hao Zhang and Ion Stoica},
  booktitle={Proceedings of the 29th Symposium on Operating Systems Principles},
  year={2023}
}
```

This project also uses the HumanEval-Java subjects released for program-repair research, and rebuilds LeetCode-Java under the protocol of MUTGEN.

```
@inproceedings{HumanEval-Java,
  title={Impact of Code Language Models on Automated Program Repair},
  author={Nan Jiang and Kevin Liu and Thibaud Lutellier and Lin Tan},
  booktitle={Proceedings of the 45th International Conference on Software Engineering},
  pages={1430--1442},
  year={2023}
}
```

<h1> 
  <p align=center> MERIT: Mutation-Exposed Weakness Ranking for Iterative Testing With Open-Source Code Large Language Models </p>
<div align="center">

![Java 11](https://img.shields.io/badge/java-11-g)
![PIT 1.15.3](https://img.shields.io/badge/PIT-1.15.3-blue.svg)
[![docs](https://img.shields.io/badge/docs-latest-blue)](README.md)

</div>
</h1>
<img src="Assets/fig1.png" width="1500">


## DataSet  
<table>
  <thead align="center">
    <tr>
      <th>Database</th>
      <th>Description</th>
      <th>Subjects</th>
      <th>Mutants</th>
      <th>Perturbed Subjects</th>
      <th>BaiduYun Download</th>
      <th>Google Download</th>
    </tr>
  </thead>
  <tbody align="center">
    <tr>
      <td><a href="https://github.com/lin-tan/clm">HumanEval-Java</a></td>
      <td align="left">Self-contained Java classes taken verbatim from the official index of the program-repair dataset. Each subject is a single algorithmic unit at the method level.</td>
      <td>104</td>
      <td>1,114</td>
      <td>50</td>
      <td>---</td>
      <td>---</td>
    </tr>
    <tr>
      <td>LeetCode-Java</td>
      <td align="left">Rebuilt from a public corpus of LeetCode solutions. 50 Medium and 50 Hard problems drawn with a fixed seed, self-contained classes only, each verified to compile in isolation.</td>
      <td>100</td>
      <td>1,829</td>
      <td>50</td>
      <td>---</td>
      <td>---</td>
    </tr>
  </tbody>
</table>

## Results  

<table>
  <thead align="center">
    <tr>
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
      <td rowspan="4">HumanEval-Java</td>
      <td>EvoSuite</td>
      <td>100.0</td><td>100.0</td><td>96.9</td><td>95.9</td><td>64.4</td><td>5.9</td><td>151.6</td><td>---</td>
    </tr>
    <tr>
      <td>EvoSuite<sub>mut</sub></td>
      <td>98.1</td><td>98.1</td><td>94.7</td><td>93.6</td><td>62.8</td><td>4.1</td><td>131.9</td><td>---</td>
    </tr>
    <tr>
      <td>Vanilla</td>
      <td>91.3</td><td>38.5</td><td>81.3</td><td>89.7</td><td>83.4</td><td>7.3</td><td>34.1</td><td>1,204</td>
    </tr>
    <tr>
      <td><b>MERIT</b></td>
      <td>95.2</td><td><b>95.2</b></td><td>85.0</td><td>93.2</td><td><b>88.8</b></td><td>11.1</td><td>105.9</td><td>3,698</td>
    </tr>
    <tr>
      <td rowspan="4">LeetCode-Java</td>
      <td>EvoSuite</td>
      <td>92.0</td><td>92.0</td><td>90.8</td><td>90.0</td><td>66.1</td><td>5.3</td><td>231.1</td><td>---</td>
    </tr>
    <tr>
      <td>EvoSuite<sub>mut</sub></td>
      <td>95.0</td><td>95.0</td><td>93.7</td><td>92.6</td><td>70.6</td><td>3.5</td><td>221.0</td><td>---</td>
    </tr>
    <tr>
      <td>Vanilla</td>
      <td>88.0</td><td>10.0</td><td>82.4</td><td>79.2</td><td>68.1</td><td>5.2</td><td>66.9</td><td>1,092</td>
    </tr>
    <tr>
      <td><b>MERIT</b></td>
      <td><b>99.0</b></td><td><b>99.0</b></td><td><b>93.9</b></td><td>90.2</td><td><b>84.3</b></td><td>11.2</td><td>196.8</td><td>6,036</td>
    </tr>
  </tbody>
</table>

- Results are obtained with the Qwen2.5-Coder-32B-Instruct backbone. $MS$ is the PIT mutation score under the DEFAULTS operator group, averaged over the subjects of each benchmark, with a failed generation scored zero.
- Each EvoSuite variant receives a per-subject search budget equal to the mean wall-clock time of MERIT on the same benchmark, 106 s on HumanEval-Java and 197 s on LeetCode-Java.
- The improvement direction also holds on the other two backbones. DeepSeek-Coder-33B-Instruct moves from 65.8 to 69.7 and from 27.3 to 34.3 in $MS$, and CodeLlama-34B-Instruct from 31.3 to 33.8 and from 13.2 to 23.0.



## Dependencies and Installation 

1. Clone and enter the repo.

   ```shell
   git clone https://github.com/wanq501/MERIT.git
   cd MERIT
   ```

2. Install dependencies

   ```shell
   pip install -e .
   ```

- Note: JDK 11 and Maven must be on PATH, matching the runtime of the test forks and of EvoSuite. JUnit 4.13.2, JaCoCo 0.8.11, PIT 1.15.3, and EvoSuite 1.2.0 are pinned in the build files and resolved on the first run. The backbone is served locally through a vLLM OpenAI-compatible server on a single GPU, so no request leaves the premises.

## Generation and Evaluation 

1. Serving

   ```shell
   vllm serve Qwen/Qwen2.5-Coder-32B-Instruct --port 8000
   ```


2. Smoke test

   ```shell
   python run_experiments.py --smoke
   ```



3. Full run

   ```shell
   python run_experiments.py --benchmark humaneval_java --backbone qwen
   python run_experiments.py --benchmark leetcode_java --backbone qwen
   ```

4. Tables and figures

   ```shell
   python generate_paper_results.py
   ```

- Note: Each script includes detailed instructions on how to set parameters and use the script properly. Always run the smoke test on a small sample before launching a full run.
- The loop defaults to at most four weakness rounds, a budget of eight weakness items per prompt, and at most six new tests per round. Every table in the paper is produced from the run records by the last script alone.


## Code  
The code will be made public shortly after the acceptance of the paper.


## Citation

If you find our repo useful for your research, please cite us:

```


```

This project builds on the open source mutation testing tool [PIT (Pitest)](https://github.com/hcoles/pitest).

```
@inproceedings{PIT,
  author={Henry Coles and Thomas Laurent and Christopher Henard and Mike Papadakis and Anthony Ventresque},
  title={PIT: a practical mutation testing tool for Java (demo)},
  booktitle={Proceedings of the 25th International Symposium on Software Testing and Analysis},
  pages={449--452},
  year={2016}
}
```


This project also utilizes the [HumanEval-Java](https://github.com/lin-tan/clm) dataset for evaluation.

```
@inproceedings{HumanEval-Java,
  title={Impact of Code Language Models on Automated Program Repair},
  author={Nan Jiang and Kevin Liu and Thibaud Lutellier and Lin Tan},
  booktitle={Proceedings of the 45th IEEE/ACM International Conference on Software Engineering},
  pages={1430--1442},
  year={2023}
}
```

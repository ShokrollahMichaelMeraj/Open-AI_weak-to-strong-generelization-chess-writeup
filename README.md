# Weak-to-Strong Generalization: Small-Scale Chess Replication (Writeup)

A writeup of our attempted small-scale replication of the chess-puzzle experiment from
[*Weak-to-Strong Generalization: Eliciting Strong Capabilities With Weak Supervision*](https://arxiv.org/abs/2312.09390)
(Burns, Izmailov et al., OpenAI, 2023). Done for CMPT 419/983 (Trustworthy Deep Learning) at Simon Fraser University.

**Status:** incomplete / ongoing. The weak supervisor mode-collapsed, and the student and ceiling results were not obtained. This document records what we tried, what happened, and why the failure is still informative.

## What the paper does

The paper asks whether a strong model can be aligned using supervision from a weaker one, as an analogy for humans supervising superhuman AI. It uses three models:

- **Weak supervisor:** a small model trained on ground-truth labels.
- **Strong ceiling:** a large model trained on the same ground-truth labels (the best case).
- **Strong student:** the same large architecture, trained only on the weak supervisor's labels.

The headline metric is **Performance Gap Recovered (PGR)**:

```
PGR = (student - weak) / (ceiling - weak)
```

PGR = 1 means the student fully matches the ceiling despite weak supervision. PGR = 0 means it only matches the weak supervisor.

## What we did

We rebuilt the three-model chess setup at much smaller scale:

| | Paper | This replication |
|---|---|---|
| Data | Lichess puzzles, Stockfish best-move labels | 200K Lichess puzzles, Stockfish best-move labels |
| Weak supervisor | GPT-2 family, pretrained | ~800K parameters, trained from scratch |
| Strong student / ceiling | Larger pretrained GPT-2 models | ~6.3M parameters, trained from scratch |
| Pretraining | Yes | No |
| Hardware | Large-scale | Kaggle 2x T4 |

Training from scratch is the biggest deviation. The paper's strong models arrive with pretrained knowledge that weak labels can "elicit"; ours do not.

## Results

The weak supervisor **mode-collapsed**:

- Test accuracy: **1.06%**
- Null or illegal moves: **~24.6%** of predictions
- Unique predicted moves: **231**, versus **1,722** in the ground truth
- ~55% of predictions fall on the f-file
- Zero malformed strings, so the model learned move *format* but not move *selection*

Student and ceiling accuracy: **not available** (the runs did not complete). No PGR value is reported.

## Why the failure is still informative

A weak supervisor that makes narrow, systematic errors is close to the failure condition discussed in Appendix E of the paper: when the weak labels' errors are trivially imitable, a strong student can simply learn to reproduce them instead of generalizing past them. Our collapse is not a clean test of the paper's claim, but it illustrates that failure mode concretely and shows how sensitive the setup is to weak-supervisor quality.

## Limitations

- Models were trained from scratch with no pretraining, so this does not test the paper's central premise.
- The three-model comparison is incomplete, so no PGR is reported.
- Single run, with no seeds or variance estimates.

## Possible next steps

- Finish the student and ceiling runs and report PGR.
- Fix the weak supervisor (more data, longer training, or a small pretrained model) so its errors are not degenerate.
- Add pretrained checkpoints to move closer to the paper's setting.

## Credits

- Paper: Burns, Izmailov, Leike, Sutskever et al., *Weak-to-Strong Generalization*, OpenAI, 2023.
- Data: [Lichess puzzle database](https://database.lichess.org/#puzzles), with labels from Stockfish.
- Project partner: [ Akki Singh ]
- Course: CMPT 419/983, Simon Fraser University.

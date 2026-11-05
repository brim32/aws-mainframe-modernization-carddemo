# 20-minute COBOL demo

Repo: [brim32/aws-mainframe-modernization-carddemo](https://github.com/brim32/aws-mainframe-modernization-carddemo)
Pull request: https://github.com/brim32/aws-mainframe-modernization-carddemo/pull/1
Review comment: https://github.com/brim32/aws-mainframe-modernization-carddemo/pull/1#issuecomment-6670731820
Fallback branch: `demo/monthly-category-fee` (this pull request is that diff)
Live edit starts on `main`. This folder is on `main`. The run-of-show file is untracked, so it is not in the pull request.

Size line, say it once: this is one COBOL application, on the order of 30 programs, not their full estate.

## 0:00 Frame

"I'll use a public COBOL credit-card application as the stand-in for your core. Screens, copybooks, and batch behind them, and no living map."

## 1:00 Cloud agent map

Start this before the meeting. Do not start it on stage. In the Agents window, choose Cloud, repo `brim32/aws-mainframe-modernization-carddemo`, branch `main`, and paste:

```
Map one path in this COBOL application. Do not edit files and do not open a pull request.

Path: batch job INTCALC (app/jcl/INTCALC.jcl) runs CBACT04C.

Reply with only:
- the paragraphs that read the rate, compute interest, and compute fees
- the copybooks CBACT04C copies
- the files the JCL uses
- the CICS bill-payment program and transaction that read the same account
- every other program that copies CVTRA02Y, CVACT01Y, and CVTRA05Y
- what breaks if 1400-COMPUTE-FEES starts charging a fee onto ACCT-CURR-BAL
```

When it finishes, it stays in the agent list. At minute 1, open that thread and walk it top to bottom. If it opens a second pull request, leave that pull request closed and do not show it. Pull request 1 stays the only GitHub page.

Talk these lines while the thread is on screen. The same map is in the pull request description.

- Batch job `INTCALC` (`app/jcl/INTCALC.jcl`) runs `CBACT04C`.
- `1200-GET-INTEREST-RATE` reads disclosure group `CVTRA02Y`. `1300-COMPUTE-INTEREST` does `(TRAN-CAT-BAL * DIS-INT-RATE) / 1200`. `1400-COMPUTE-FEES` was an empty stub.
- Files: `TCATBALF`, `DISCGRP`, `ACCTFILE`, `XREFFILE`, output `TRANSACT`.
- Online reader of the same account: CICS transaction `CB00`, program `COBIL00C`, map `COBIL00`.
- `CVTRA02Y` is copied only by `CBACT04C`. `CVACT01Y` and `CVTRA05Y` are also copied by bill payment and by daily post `CBTRN02C`.

## 5:00 Local agent

Paste this on `main`:

```
In app/cbl/CBACT04C.cbl only, implement paragraph 1400-COMPUTE-FEES. It is currently a stub.

When this paragraph runs, charge a monthly category fee of 0.25 percent of TRAN-CAT-BAL: (TRAN-CAT-BAL * 0.25) / 100. Add that fee to WS-TOTAL-INT so 1050-UPDATE-ACCOUNT posts it onto ACCT-CURR-BAL with the interest. Write a separate transaction, category 06, description starting with "Fee for a/c", using the same transaction layout as 1300-B-WRITE-TX.

Name every copybook this program copies and every other program that copies those books. Do not change JCL, BMS, or any copybook. Do not claim the program compiles.
```

Point at the files it opens, then the diff. Say there is no COBOL compiler in the room. The proof is the diff and the caller list.

At minute 11, if it is still running, open the pull request and show that diff.

## 12:00 Pull request

Open https://github.com/brim32/aws-mainframe-modernization-carddemo/pull/1

Show `CBACT04C.cbl`, then the comment already under it: programs changed, copybooks changed (none), and the other programs that copy `CVTRA02Y`, `CVTRA01Y`, `CVACT01Y`, `CVACT03Y`, and `CVTRA05Y`.

## 16:00 Automation

Open the saved automation **Copybook blast-radius review**. Show only:

- Trigger: a pull request is opened on `brim32/aws-mainframe-modernization-carddemo`
- Instruction: if the diff touches `app/cbl` or `app/cpy`, comment with the programs changed, the copybooks changed, and which other programs copy those books. Otherwise stop. Do not modify code and do not approve.

The Automations editor was opened with that draft. Save it if the form is still unsaved. The comment on pull request 1 is the review text for the stage. Do not fill out a new automation during the meeting.

## Close

A two-week POC on one COBOL program from their estate. Success is a map from the code, one reviewed change, and this comment on every pull request that touches that path. Suggestion accept-rate is the wrong score for the last Copilot trial.

If they ask who owns it: their core-system owner, with you on the first program. If they mention a lab: a lab can draft code; this runs in the repository, opens a pull request, and reviews the next one. If they mention VB6: the same review, on one of their modules, during the POC.

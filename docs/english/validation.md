# Validation and Current Status

## Validation Scope

The available evidence combines implementation review, selected automated tests, project notes, and my current validation set. These sources show that the processing and email components exist and that specific paths have been exercised. They do not form a production performance study. In particular, mocked tests establish expected behavior under simulated responses; they do not prove live email delivery, file storage, or organizational adoption.

## Demonstrated Capabilities

The document-processing service extracts text from PDFs, requests structured invoice fields from a language model, applies catalog and arithmetic checks, and produces an inspection spreadsheet. The email workflow selects eligible unread messages and has paths for processed documents, rejections, and failures. Service smoke tests cover basic request and health behavior; workflow tests cover selected message selection, attachment, upload, and routing paths with mocks. These are bounded checks, not full coverage of every invoice or integration.

The email flow works through my own account. Five real invoices are in my current validation set, but the criteria for calling an invoice successful are still being documented. The available records do not establish five verified end-to-end completions or a five-out-of-five success rate. Internal project notes also describe repeated comparisons involving four distinct invoice examples; the source documents, reference outputs, and per-run records are not available for an independent audit here. Repeated runs on those examples should not be treated as independent layout coverage.

## Performance Measurements

Earlier controlled averages were reported without enough supporting records to establish their sample size and measurement boundaries; those figures remain omitted. The October 2026 comparison below is a separate, documented experiment. Its service processing time and estimated variable cost are not the full email-to-storage cycle time or total operating cost.

## Controlled Performance Benchmark — October 2026

An internal KPI report compares Haiku 4.5 and Haiku 5.5 on **four distinct, previously used invoice examples**. Each invoice was run five times per model: **20 service requests per model, 40 in total**, tested on the same day. The invoices were part of the development set, not unseen test data. Extracted codes, quantities, and dates were compared with human-reviewed reference outputs; a successful request means the processing service returned an output, not that the email workflow or a human approval step completed.

| Metric | Haiku 4.5 | Haiku 5.5 |
| --- | ---: | ---: |
| Mean processing time | 16.1s | 6.9s |
| Estimated variable cost per invoice | $0.01766 | $0.00233 |
| Correct code/quantity results | 510/510 | 510/510 |
| Correct invoice dates | 19/20 | 20/20 |
| Successful requests | 20/20 | 20/20 |

Processing time was measured **inside the service request handler**, from PDF reading through model interpretation, catalog checks, and spreadsheet generation. It excludes email orchestration and delays before processing starts. Estimated variable cost combines model API charges with an estimate of request-processing compute. It excludes the email job, storage, builds, logging, idle or minimum-instance charges, and other operating costs; it is not a cloud bill or a projected return on investment.

On these tested examples, Haiku 5.5 had approximately **57% lower mean processing time** and **87% lower estimated variable cost**. Both models returned correct code/quantity values in all scored line evaluations, so no regression was observed on that measure in this sample. The newer model made **one fewer invoice-date error** (20/20 versus 19/20). These are repeated observations on four invoices, not estimates of performance on new suppliers or layouts.

The **510/510** figure is **102 distinct invoice lines evaluated five times per model**, not 510 independent invoices. No unseen invoice layout was tested. Supplier-name capitalization changed in some runs, which can affect the generated header or filename even when the supplier was identified. No automatic warnings were raised in these runs; those checks do not catch every kind of extraction error. The comparison did not re-run downstream email orchestration and does not establish generalized accuracy, production reliability, employee time savings, or ROI. The report summarizes the measurements, while source invoices and per-run artifacts remain private and are not included in this repository.

A separate **single-case degraded-input stress test** deliberately used a jumbled text layout from one previously used invoice. Across five runs per setting, the older model had 83/130 correct code/quantity evaluations and the newer model's tested settings had 130/130. These were direct model-API calls under altered input, not the standard service workflow or evidence about unseen layouts. Under the normal two-layout input, the older model also scored 130/130 on that invoice.

## Limits and Deployment Status

The current work does not establish generalized extraction accuracy, production reliability, throughput, adoption, the rate of human intervention, manual time saved, error reduction, or return on investment. No measured manual baseline is available for a savings claim. A generated spreadsheet can carry warnings, and the workflow sends that output before human signoff; its existence alone is not proof that the invoice was ready for operational use.

The system works through my own account. Organizational inbox configuration and internal rollout remain pending. Formal warning-review and approval ownership still need to be defined for the deployed process. This case study does not describe the workflow as production-deployed.

## Next Validation Steps

The next work is to define invoice-level success criteria and record outcomes for each document, including rejections, warnings, retries, and manual intervention. Testing should include more varied, unseen invoice layouts and a review of the generated output against the source. Per-run runtime, token, and cost inputs should be retained so the October comparison can be independently reproduced and extended to the full email flow. Before organizational rollout, the team should establish exception-review ownership, test the organizational inbox path, and confirm how warning-marked outputs may be used. These are proposed steps, not completed results.

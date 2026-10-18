# Design Decisions

The process problem came first: one person had to interpret an incoming document, consult product information, prepare an inspection record, keep the files together, and respond. The design separates steps that need interpretation from those that should follow explicit rules. These choices support a working process while leaving important operational controls to finish before internal rollout.

## Use AI for interpretation

Invoices do not share one layout or wording pattern. Fixed positions and rigid extraction rules can fail when the same information appears in a different place. The service therefore extracts PDF text and asks a language model to classify the document and propose structured fields. This flexibility is useful, but a structured model response is still a proposal. It does not prove that a value matches the original document.

## Use rules for checks

Product-code lookup and line arithmetic have defined inputs and repeatable outcomes. Application logic performs these checks so a given set of extracted values can be evaluated predictably. Asking the model to decide whether a catalog code exists or whether numbers reconcile would make those decisions harder to trace. A failed lookup or quantity check creates a visible warning rather than quietly changing the invoice value. Catalog coverage and the quality of the extracted inputs still limit what those checks can establish.

## Keep people in the exception path

An unusual quantity or missing catalog value can require knowledge of the transaction. The person responsible for the invoice workflow is the intended reviewer of warnings; persistent failures and unexpected formats also need human follow-up. Formal approval ownership and signoff have not yet been defined. In the current implementation, a warning-marked spreadsheet is stored and sent without waiting for approval. That trade-off keeps the routine flow moving but leaves a control gap to resolve before organizational use.

## Preserve the document relationship

The source PDF, inspection spreadsheet, stored copies, and reply belong to one operational case. Filing the source and output together and responding in the original email thread helps the reviewer trace an output back to its input and find the related conversation. This is practical traceability, not a claim of a complete audit trail or transactional storage: a later failure can still interrupt a sequence of actions.

## Route exceptions according to the result

A rejected document receives an explanatory reply without a spreadsheet. A processing failure leaves the message available for a later attempt; a service failure also alerts operations. Warnings travel with an otherwise generated output so the questionable field is visible to the recipient. These paths avoid presenting every exception as a completed invoice, but they do not yet guarantee that someone has reviewed a warning.

## Current trade-offs

The workflow favors a clear separation of interpretation, checks, and response paths over an all-purpose AI decision. It has no enforced warning-approval gate, and it has not established accuracy across diverse suppliers or reliable recovery from every partial failure. Those limits matter to the operational design and are part of the remaining internal rollout work.

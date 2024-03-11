# Raven steps

| Step  | What it does                               | Should it be a notebook                                                                                          |   |    |
| ----- | --------                                   | -----------------------                                                                                          |   |    |
| 1     | Generate names                             | No, can be added to a config file                                                                                |   |    |
| 2     | Add employee ids to allocation cells       | Still in a config file                                                                                           |   |    |
| 3     | Validate price changes                     | Yes (for now)                                                                                                    |   |    |
| 4A    | Construct policies for control to DW       | No, it looks like step 4 is making a table on the data warehouse, this could be consildated into a single script |   |    |
| 5A    | Forklift control policies AND verification | No, and should be split                                                                                          |   |    |
| 5B    | Forklift test plicies AND verificatoin     | Ditto                                                                                                            |   |    |
| 6     | Create links in spreadsheet                | No                                                                                                               |   |    |
| 7     | Marshel data to price change events        | No, sets base prices in FashionThing                                                                             |   |    |
| 8     | Send data to FashionThing                  | No, lots of cleanup                                                                                              |   |    |
| 9     | Revert                                     | ???                                                                                                              |   |    |
|       |                                            |                                                                                                                  |   |    |

## Principles

- We should be able to re-run any step, if it is a step that should only occur once then the script should exit early without performing the non-idempotent action
- We should generate logs of the output for inspection
  - Unclear if those logs should be saved to github, or just to the data warehouse
- Where possible, constants should be stored in a single top-level file
  - We might create an additional file for data created (e.g. plan-id), rather than append it to retain "purity"
- We should only use notebooks where they offer a compelling advantage. Such situations are 
  - When graphical context (e.g. histograms, bar charts, etc) offer an essential part of what is being offered
  - There is a large degree of customization and verification, which would be hard to standardize
  - The interactive element is immportant, as is capturing the output of the final run
- We should be able to verify a step as being complete

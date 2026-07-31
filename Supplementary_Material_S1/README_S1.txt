Supplementary Material S1
The Effect of Unit and Range Information on Large Language Model-Based
Building Energy Parameterization

Contents
--------

1. prompt_templates/
   - f1_prompts.json  : Format F1 (minimal) prompts sent to both LLMs,
                         one entry per building x climate.
   - f2_prompts.json  : Format F2 (unit only) prompts.
   - f3_prompts.json  : Format F3 (range only) prompts.
   - f4_prompts.json  : Format F4 (unit + range, fully constrained)
                         prompts.
   Each entry records the exact system_prompt text sent to the model
   API for that building/climate/format combination, sufficient to
   reproduce the exact prompts used during data collection. Parameter
   keys (param_1..param_9) are neutral and schema-fixed, as described
   in Section 3.3 of the manuscript.

2. lobo_range_table.json
   The full numerical Leave-One-Building-Out (LOBO) range table: for
   each of the 5 tested buildings and each of the 9 target parameters,
   the derived [lower, upper] bound used to construct the F3/F4
   prompts (Section 3.3 of the manuscript), together with the
   governing physical bounds, the buffer method, and the list of
   target/control parameters.

3. model_api_config.json
   The exact model and API configuration used for Sprint 3 (LLM
   Parameter Generation): model identifiers as resolved at call time,
   and the per-model decoding parameters (temperature, max_tokens,
   reasoning_effort, top_p, frequency/presence penalty, response
   format, timeout) referenced in Section 4 ("LLM Configuration and
   Simulation") and discussed as a limitation in Section 6
   ("Model-family effect").

Provenance
----------
These files are extracted, without modification, from the code and
data repository accompanying this manuscript:
https://github.com/elifylmaz/bem-llm-unit-range
(paths: analiz/sprint3_prompts/f*_prompts.json,
analiz/sprint2_lobo/lobo_range_table.json,
data/environment_sprint3.json)

The repository also contains the full Sprint 1-6 Jupyter notebooks,
raw LLM outputs, injected IDF files, and EnergyPlus simulation logs,
which are not duplicated here due to their size (several GB) but are
openly available at the link above.

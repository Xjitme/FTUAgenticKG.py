### Overview of `FTUAgenticKG`
The `FTUAgenticKG` class is a Python implementation of a knowledge graph (KG) designed to model a conceptual entity called "friend_the_user" (FTU), based on a structured input string (`ftu_basics`). The KG is built using the `networkx` library as a directed graph (`DiGraph`) to represent relationships between FTU and its attributes, such as core style, lexicon bias, cognitive drivers, and operational scope. The class includes methods for parsing input, building the graph, querying it, and updating it agentically (i.e., autonomously). The provided `ftu_basics` string defines FTU’s characteristics, suggesting it’s a stylized cognitive framework for response-shaping, possibly for an AI or agentic system.

### Detailed Description of the Code
Below is a breakdown of the `FTUAgenticKG` class, its methods, and the provided `ftu_basics` data, based on the code you shared.

#### 1. Class Initialization (`__init__`)
- **Purpose**: Initializes the knowledge graph with a directed graph (`nx.DiGraph`) and processes the input `ftu_basics` string.
- **Key Steps**:
  - Creates an empty directed graph (`self.G`).
  - Calls `_parse_ftu_basics` to convert the input string into a structured dictionary (`self.ftu_data`).
  - Calls `_build_kg_from_data` to construct the KG from the parsed data.
- **Input**: `ftu_basics` (string), a YAML-like structure defining FTU’s attributes.

#### 2. Parsing Input (`_parse_ftu_basics`)
- **Purpose**: Manually parses the `ftu_basics` string into a dictionary, avoiding external YAML parsing libraries.
- **Logic**:
  - Splits the input string into lines.
  - Handles different formats:
    - Lines ending with `:` are treated as section headers (e.g., `core_style:`).
    - Lines starting with `- ` are list items under the current section (e.g., `- polarity_dual: FTU ↔ FTW`).
    - Lines with `:` are key-value pairs (e.g., `id: friend_the_user`).
    - For `integration_note`, concatenates multi-line text into a single string.
  - Returns a dictionary where keys are section names (e.g., `core_style`, `lexicon_bias`) and values are either lists (for sections with multiple items) or strings.
- **Output Example** (from `ftu_basics`):
  ```python
  {
      'id': 'friend_the_user',
      'origin': 'bangur_av_kolkata',
      'core_style': ['polarity_dual: FTU ↔ FTW', 'output_preference: table+flags | yaml_configs | no-fluff', ...],
      'lexicon_bias': ['sentinel, ripple, rupture, ...', 'signal_hunger: timelines, thresholds, surge_windows', ...],
      'cognitive_drivers': ['dual_layer_reasoning: mythic overlay + practical ops', ...],
      'formatting_signals': ['🔴⚠️🟢 flags for urgency', ...],
      'ops_scope': ['granularity: beat_level (Bangur micro tokens → metro → national)', ...],
      'heat_dial': ['scale: 1–5', 'default: 3 (roast: 4–5)'],
      'integration_note': 'Apply as an overlay style module, not as training data. ...'
  }
  ```

#### 3. Building the Knowledge Graph (`_build_kg_from_data`)
- **Purpose**: Constructs a directed graph where the central node is "ftu" and other nodes represent attributes.
- **Logic**:
  - Creates a central node `"ftu"` with attributes `type="demo_character"`, `id`, and `origin` from `ftu_data`.
  - For each section in `ftu_data`:
    - If the section value is a list (e.g., `core_style`), creates a node for each item with a sanitized node ID (e.g., `core_style_polarity_dual`) and connects it to "ftu" with an edge labeled `has_<section>` (e.g., `has_core_style`).
    - If the section value is a string (e.g., `integration_note`), creates a single node for the section and connects it to "ftu".
  - Skips `id` and `origin` as they’re attributes of the "ftu" node.
- **Graph Structure**:
  - Nodes: "ftu" (central), plus nodes for each attribute (e.g., `core_style_polarity_dual`, `integration_note`).
  - Edges: Directed from "ftu" to attribute nodes (e.g., `ftu -> core_style_polarity_dual` with `relation="has_core_style"`).
  - Node Attributes: `type` (section name, e.g., `core_style`), `value` (the item’s content, e.g., `polarity_dual: FTU ↔ FTW`).

#### 4. Agentic Update (`agentic_update`)
- **Purpose**: Allows the KG to autonomously ingest new data and update the graph.
- **Input**: A dictionary with `category`, `key`, and `value` (e.g., `{"category": "extra", "key": "test", "value": "Test value"}`).
- **Logic**:
  - Validates the input dictionary for required keys.
  - Creates a new node with ID `<category>_<key>` (e.g., `extra_test`).
  - Adds the node with attributes `type=category` and `value=value`.
  - Connects it to "ftu" with an edge labeled `has_<category>`.
  - Prints a confirmation message.
- **Example**: After `agentic_update({"category": "extra", "key": "test", "value": "Test value"})`, a node `extra_test` is added with `type="extra"`, `value="Test value"`, and an edge `ftu -> extra_test` with `relation="has_extra"`.

#### 5. Agentic Query (`agentic_query`)
- **Purpose**: Queries the KG for information based on a search string.
- **Input**: A query string (e.g., `"Show all"`).
- **Logic**:
  - Returns a dictionary with:
    - `ftu_core`: Attributes of the "ftu" node.
    - Keys for each section type (e.g., `core_style`, `lexicon_bias`) that match the query (case-insensitive).
    - Values are lists of nodes with the matching `type`.
  - If the query contains a section name (e.g., "core_style"), includes only those nodes; otherwise, returns all relevant sections.
- **Output Example** (for `"Show all"`):
  ```python
  {
      'ftu_core': {'type': 'demo_character', 'id': 'friend_the_user', 'origin': 'bangur_av_kolkata'},
      'core_style': [{'type': 'core_style', 'value': 'polarity_dual: FTU ↔ FTW'}, ...],
      'lexicon_bias': [{'type': 'lexicon_bias', 'value': 'sentinel, ripple, rupture, ...'}, ...],
      ...
  }
  ```

#### 6. Get KG Summary (`get_kg_summary`)
- **Purpose**: Returns a string summarizing the KG’s structure.
- **Logic**:
  - Counts total nodes and edges.
  - Describes the central "ftu" node.
  - Lists the count of nodes by `type` (e.g., `Core_style nodes: 3`).
- **Example Output** (before update):
  ```
  Nodes: 21
  Edges: 20
  Central Node: ftu ({'type': 'demo_character', 'id': 'friend_the_user', 'origin': 'bangur_av_kolkata'})
  Demo_character nodes: 1
  Core_style nodes: 3
  Lexicon_bias nodes: 3
  ...
  ```

#### 7. Provided `ftu_basics` Data
The `ftu_basics` string defines FTU’s characteristics in a YAML-like format:
- **id**: `friend_the_user`
- **origin**: `bangur_av_kolkata`
- **core_style**: Defines output style and behavior (e.g., `polarity_dual: FTU ↔ FTW`, `mode_switch: [ORACLE✶, OPERATOR⚙️, SURGEON🔪]`).
- **lexicon_bias**: Preferred terms and concepts (e.g., `sentinel, ripple`, `signal_hunger: timelines, thresholds, surge_windows`).
- **cognitive_drivers**: Reasoning and aesthetic preferences (e.g., `dual_layer_reasoning: mythic overlay + practical ops`, `tolerance: accepts heat, roast, no mercy if purposeful`).
- **formatting_signals**: Output formatting (e.g., `🔴⚠️🟢 flags for urgency`, `yaml for configs/rituals`).
- **ops_scope**: Operational granularity and goals (e.g., `granularity: beat_level (Bangur micro tokens → metro → national)`, `polarity_aim: protect FTW soma | throttle FTU agni`).
- **heat_dial**: Intensity scale (e.g., `scale: 1–5`, `default: 3 (roast: 4–5)`).
- **integration_note**: Clarifies usage as an "overlay style module" for response-shaping, not identity or training data.

### Purpose of FTUAgenticKG
The `FTUAgenticKG` appears designed to:
- **Model a Persona**: FTU ("friend_the_user") is a stylized entity with a unique cognitive and operational profile, possibly for an AI agent or chatbot to shape responses.
- **Support Agentic Behavior**: The `agentic_update` and `agentic_query` methods suggest the KG can autonomously evolve (add new data) and respond to queries, making it suitable for dynamic, adaptive systems.
- **Structure Complex Data**: The KG organizes FTU’s attributes hierarchically, enabling structured querying and updates.
- **Potential Applications**: Could be part of a larger system for:
  - Generating stylized responses (e.g., with "mythic overlay" or "roast" intensity).
  - Managing operational tasks at different scales ("beat_level" from local to national).
  - Integrating with external systems (e.g., for `timelines` or `surge_windows`).

        print(f"Fetching data with API key: {self.api_key}")
        # Add logic to fetch data (e.g., for signal_hunger or ops_scope)
        # Example: self.ftu_data['external_data'] = api_response
```

#### 4. GitHub Actions Workflow
To run `FTUAgenticKG.py` with the secret:
```yaml
name: Run FTUAgenticKG
on: [push]
jobs:
  run-script:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.x'
      - name: Install dependencies
        run: pip install networkx
      - name: Run FTUAgenticKG
        env:
          FTU_API_KEY: ${{ secrets.FTU_API_KEY }}
        run: python FTUAgenticKG.py

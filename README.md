# Peptide Matching Problem, Gao lab bioinformatics subgroup discussion

**Topic for discussion**: search sequence in fasta
**Problem description**: A fasta file typically contains thousands of protein sequence, and how to quickly match millions or even billions of peptide sequences to a fasta file is a critical task in proteomics.

**Goal**: minimize the runtime of the following function
 
function search_pep_seq(peptide_seq_list, long_combined_seq) -> peptide_seq_pos_list [(start, end), (start, end), ...]
 
**Example**: 
input: search_pep_seq(['abc', 'def', 'efgh', 'xyz', 'xxx'], 'abcdefgh') -> [(0, 2), (3, 5), (4, 7), (-1, 1)], with non-exist seq, use -1 as start, end can be any and will be ignored.
 
The example.7z file, once extracted, will give you an example.json file, which contains three keys, use the first two as your input and check with the expected output in results, time it and see if you can beat the standard solution.
 
{'peptide_list': peptide_list, 'long_seq': long_seq, 'results': positions}
 
 
**Example standard solution:**
```python
import ahocorasick

def build_automaton(peptide_list):
    peptide_list = list(set(peptide_list))
    A = ahocorasick.Automaton()
    for idx, peptide in enumerate(peptide_list):
        A.add_word(peptide, (idx, peptide))
    A.make_automaton()
    return A

def find_peptide_positions(long_seq, automaton):
    positions = defaultdict(list)
    for end_index, (idx, peptide) in automaton.iter(long_seq):
        start_index = end_index - len(peptide) + 1
        result = (start_index, end_index)
        positions[peptide].append(result)
    return positions
not_found_peptides = [peptide for peptide in peptide_list if peptide not in positions]

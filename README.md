# QuikChange primer design

A web tool (a single HTML file, nothing to install) for designing site-directed mutagenesis primers following the QuikChange II Site-Directed Mutagenesis Kit instruction manual (Agilent, Revision E.0).

## Usage

Open `index.html` in a browser, paste the sequence (FASTA accepted), choose the mutation type and click **Design primers**.

| Type | Format | Example |
|---|---|---|
| Nucleotide substitution | original base, position, new base | `A123G` |
| Amino acid substitution | original aa, position, new aa (`*` = stop) | `L45A` |
| Insertion | position (insert after it), sequence | `123 GATTACA` |
| Deletion | first-last position | `123-125` |

For amino acid substitutions, enter the position of the A of the ATG in the pasted sequence. The tool picks the new codon with the fewest nucleotide changes.

### Sequence and positions

- Paste the full gene or plasmid if possible. A fragment also works if it has at least 60 nt, ideally 30 to 50 bases on each side of the mutation. The primer itself uses about 10 to 22 bases on each side.
- Positions are counted from the first pasted base (position 1). With a fragment, `A123G` refers to the 123rd base of the fragment, not of the gene.
- For amino acid substitutions, enter the position of the A of the ATG within the pasted sequence. If the fragment starts elsewhere in the gene, use a nucleotide-level mutation instead.
- The tool checks that the original base or amino acid matches your sequence and warns you if it does not.
- Paste the 5′→3′ strand, the same one as the forward primer. The reverse primer is its reverse complement.
- The tool picks the shortest primer that meets all criteria. Your own primer can be longer or placed differently and still be valid, so compare Tm, GC, length and G/C ends rather than the exact sequence.

### Writing an amino acid mutation

- Choose **Amino acid substitution** and write the original amino acid, the residue number and the new one. To change cysteine 45 to alanine, write `C45A`.
- Residues are counted from the ATG, where the methionine is residue 1. Enter the position of the A of that ATG in the pasted sequence. Example: if the ATG starts at position 250 and the cysteine is residue 45, write `C45A` and `250`.
- The tool reads the codon of that residue, checks that it encodes the original amino acid and warns you if it does not. It then picks the new codon with the fewest base changes (for example, TGC to GCC for Cys to Ala).
- If your plasmid has a tag before the gene, or your protein numbering does not start at the ATG of the pasted sequence, count from the first codon of the numbering you use and enter the position of that codon.
- If you already know where the residue sits in the pasted sequence (for example, position 996 of the plasmid), fill in the optional "position of the residue" field and write only the residue number in the mutation, such as `C50A`. The tool finds the codon near that position, checks that it encodes the original amino acid and ignores the ATG position. The codon it used is shown in the result.
- If the numbering is complicated, use **Nucleotide substitution** instead, for example `T123G`.

## Criteria applied

- Primers of 25 to 45 nt, with at least 10 nt of correct sequence on each side of the mutation
- Tm ≥ 78 °C: `Tm = 81.5 + 0.41(%GC) − 675/N − %mismatch` (%GC and %mismatch as whole numbers)
- For insertions, N excludes the inserted bases. For deletions, there is no mismatch term
- GC ≥ 40% and G/C at both ends
- ng to pmol conversion using 330 g/mol per base (125 ng of a 25-mer ≈ 15 pmol)

When no candidate meets every criterion, the tool returns the best one available and flags what is missing.

## Melting temperature and annealing

- **Tm of each primer** by nearest-neighbor: `Tm = 1000·ΔH / (ΔS + R·ln(C/4)) − 273.15`, with SantaLucia & Hicks (2004) parameters and the SantaLucia (1998) salt correction: `ΔS(Na+) = ΔS(1 M) + 0.368·(N − 1)·ln[Na+]`. Defaults: 50 mM Na+ and 250 nM primer, the assumptions described by SnapGene. A calibration offset field lets you match the Tm to SnapGene.
- **Ta for Taq:** Tm − 5 °C (test range Tm − 5 to Tm).
- **Ta for Phusion:** Tm + 6 to 12 °C, the rule from SnapGene’s documentation for polymerases with a dsDNA-binding domain (Phusion, Phire, Q5). Thermo’s calculator uses modified Breslauer with Ta = Thermo Tm + 3 °C for primers > 20 nt; that method is not implemented here.
- The nearest-neighbor Tm is not comparable to the Tm ≥ 78 °C of the QuikChange formula, which is a different scale.
- Internal mismatches, loops, Mg2+, dNTPs and DMSO are not modeled. For primers with mismatches, insertions or deletions, the Tm shown assumes perfect pairing and is higher than the value SnapGene reports (which accounts for the mismatches). The page warns about this. In a Cys to Ala test (TGC to GCC, 2 mismatches), the tool showed 67.2 °C and SnapGene 59 °C. Use SnapGene’s Tm for the first PCR cycles, or enter the difference in the calibration offset.

## Order to the supplier

The tool generates the two order lines (name and sequence, tab-separated for pasting into a spreadsheet) and shows the lab’s standard configuration: 0.025 µmol, desalt, dry or in TRIS, 100 µM, 1 tube per primer. It also calculates resuspension (10 µL per nmol for 100 µM) and the volume per reaction for 125 ng of primer, using a 1:10 dilution.

Note: the QuikChange II manual calls for primers purified by FPLC or PAGE, and desalting is not that purification. The tool warns about this on the page.

## Limitations

- The sequence is treated as linear (no wrap-around for circular plasmids)
- No check for secondary structure or primer dimers
- Check the Tm against Agilent’s online calculator before ordering

## References

- SantaLucia J Jr (1998) PNAS 95:1460–1465.
- SantaLucia J Jr, Hicks D (2004) Annu Rev Biophys Biomol Struct 33:415–440.
- SnapGene: https://support.snapgene.com/hc/en-us/articles/10242720294036 and https://support.snapgene.com/hc/en-us/articles/10242720499220
- Thermo Scientific: Important instructions on calculating PCR annealing temperatures (Phusion, Phire).
- Agilent Technologies. QuikChange II Site-Directed Mutagenesis Kit, Instruction Manual, Catalog #200523 (10 reactions) and #200524 (30 reactions), Revision E.0.

## Publish with GitHub Pages

In *Settings → Pages*, choose the `main` branch and the root folder. The tool will be available at `https://YOUR-USERNAME.github.io/REPO-NAME/`.

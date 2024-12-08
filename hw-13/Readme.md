
Assignment 13

### RNA-Seq Analysis Workflow

#### Download reference genome and DTF file 

```bash
make genome 
```

#### Download SRA reads

```bash
make download
```

#### Index genome 

```bash
make index
```

#### Quantify reads

```bash
make quantification
```

#### generate count matrix

```bash
make call_counts
```

#### clean intermediate files

```bash
make clean
```

#### All Steps

```bash
make all
```


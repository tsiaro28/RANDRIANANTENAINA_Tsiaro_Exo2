def karnaugh_simplification(truth_table):
    minterms = []
    for i, val in enumerate(truth_table):
        if val:
            minterms.append(i)
    
    num_variables = len(truth_table).bit_length() - 1
    rows = 2 ** (num_variables // 2)
    cols = 2 ** (num_variables - num_variables // 2)

    # Création de la grille de Karnaugh
    karnaugh_map = [[0] * cols for _ in range(rows)]

    # Remplissage de la grille de Karnaugh
    for minterm in minterms:
        row = minterm // cols
        col = minterm % cols
        karnaugh_map[row][col] = 1

    # Regroupement des minterms adjacents
    groups = []
    for i in range(rows):
        for j in range(cols):
            if karnaugh_map[i][j] == 1:
                group = [(i, j)]
                karnaugh_map[i][j] = 0
                # Ajout des minterms adjacents horizontalement
                if karnaugh_map[i][(j + 1) % cols] == 1:
                    group.append((i, (j + 1) % cols))
                    karnaugh_map[i][(j + 1) % cols] = 0
                # Ajout des minterms adjacents verticalement
                if karnaugh_map[(i + 1) % rows][j] == 1:
                    group.append(((i + 1) % rows, j))
                    karnaugh_map[(i + 1) % rows][j] = 0
                # Ajout des minterms adjacents en diagonale
                if karnaugh_map[(i + 1) % rows][(j + 1) % cols] == 1:
                    group.append(((i + 1) % rows, (j + 1) % cols))
                    karnaugh_map[(i + 1) % rows][(j + 1) % cols] = 0
                groups.append(group)

    # Simplification des groupes
    simplified_expr = []
    for group in groups:
        terms = []
        for row, col in group:
            term = []
            for i in range(num_variables // 2):
                if row & (1 << i):
                    term.append(f"A{i}")
                elif not (row & (1 << i)):
                    term.append(f"NOT A{i}")
            for i in range(num_variables - num_variables // 2):
                if col & (1 << i):
                    term.append(f"B{i}")
                elif not (col & (1 << i)):
                    term.append(f"NOT B{i}")
            terms.append("".join(term))
        simplified_expr.append(" + ".join(terms))

    return " + ".join(simplified_expr)


def get_truth_table(num_variables):
    print(f"Entrez la table de vérité (0 pour False, 1 pour True) pour {2**num_variables} combinaisons, en séparant chaque valeur par un espace :")
    truth_table_input = input().strip().split()
    try:
        truth_table = [int(val) for val in truth_table_input]
        if len(truth_table) != 2 ** num_variables:
            raise ValueError(f"La longueur de la table de vérité doit être {2 ** num_variables}.")
        return truth_table
    except ValueError as e:
        print("Erreur:", e)
        return get_truth_table(num_variables)


# Main
num_variables = int(input("Entrez le nombre de variables : "))
truth_table = get_truth_table(num_variables)
simplified_expression = karnaugh_simplification(truth_table)
print("Expression simplifiée :", simplified_expression)

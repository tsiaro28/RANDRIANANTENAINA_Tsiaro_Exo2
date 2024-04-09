from itertools import product

# Définir la fonction logique
def fonction_logique(A, B, C):
    return (A and B ) or (not C)

# Générer le tableau de Karnaugh avec des en-têtes en code de Gray
def tableau_karnaugh_gray(func):
    print("Tableau de Karnaugh en code de Gray:")
    print("\t B'C'\t B'C \t BC \t BC'")
    for i in range(4):
        gray_i = (i >> 1) ^ i
        a, b = gray_i & 2, gray_i & 1
        a_val = 1 if a else 0
        b_val = 1 if b else 0
        print(f"A={a_val}{b_val}\t {int(not a and not b)}\t {int(not a and b)}\t {int(a and b)}\t {int(a and not b)}")

# Trouver la fonction minimisée de la fonction logique
def fonction_minimisee(func):
    termes = []
    for a, b, c in product([True, False], repeat=3):
        if func(a, b, c):
            termes.append(''.join([f"{var}" if val else f"~{var}" for var, val in zip(['A', 'B', 'C'], [a, b, c])]))
    
    # Simplification des termes
    simplified_terms = []
    while termes:
        term = termes.pop(0)
        simplified_term = term
        for other_term in termes[:]:
            different_bits = [i for i, (x, y) in enumerate(zip(term, other_term)) if x != y]
            if len(different_bits) == 1:
                index = different_bits[0]
                simplified_term = term[:index] + '-' + term[index + 1:]
                termes.remove(other_term)
                break
        simplified_terms.append(simplified_term)
    
    return ' + '.join(simplified_terms)

# Afficher le tableau de Karnaugh en code de Gray
tableau_karnaugh_gray(fonction_logique)

# Trouver et afficher la fonction minimisée
print("\nFonction minimisée de la fonction logique:")
fonction_min = fonction_minimisee(fonction_logique)
print(fonction_min)

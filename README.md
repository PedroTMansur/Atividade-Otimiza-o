# Atividade-Otimiza-o

**Aluno**: Pedro Tonidandel Mansur

**Professor**: Professor Montanha

**Nesta atividade irei mostrar a otimização de planejamento e controle de produção das máquinas de uma determinada empresa com o objetivo de mudar a velocidade da produção corrigindo restrições, como**: A capacidade de produção é limitada, as matérias-primas podem não estar sempre disponíveis para compra, o tempo necessário para a entrega das matérias-primas após a compra pode variar, existe um limite para a quantidade de matérias-primas que podem ser armazenadas de uma vez, a qualidade das matérias-primas pode variar, o custo das matérias-primas pode variar, a quantidade de mão de obra disponível pode ser limitada, as práticas de produção devem estar em conformidade com as regulamentações ambientais.

**O problema anunciado consiste em P.**, pois representa os problemas que podem ser resolvidos eficientemente (em tempo polinomial). Na prática, problemas em P são considerados "tratáveis" e "práticos" para a computação, enquanto problemas fora de P podem ser intratáveis para entradas grandes. Exemplos: Multiplicação de Matrizes e Ordenação.

**Algoritmos**:

**Constante** Um algoritmo com complexidade O(1) tem tempo de execução que não depende do tamanho da entrada.

**Logarítmica** Um algoritmo com complexidade O(log n) reduz o problema pela metade a cada iteração.

**Linear** Um algoritmo com complexidade O(n) tem tempo de execução proporcional ao tamanho da entrada.

**Linearítmica** Algoritmos com complexidade O(n log n) são geralmente algoritmos de ordenação eficientes. 

**Quadrática** Algoritmos com complexidade O(n^2) geralmente envolvem dois laços aninhados.

**Cúbica** Algoritmos com complexidade O(n^3) envolvem três laços aninhados.

**Exponencial** Algoritmos com complexidade O(2^n) têm tempo de execução que cresce exponencialmente com o tamanho da entrada.

**Fatorial** Algoritmos com complexidade O(n!) são extremamente ineficientes para entradas maiores.

**O algoritmo escolhido para a resolução do problema é o Fatorial**

def permutations(arr):

    if len(arr) == 0:
    
        return []
        
    if len(arr) == 1:
    
        return [arr]
        
    l = []
    
    for i in range(len(arr)):
    
        m = arr[i]
        
        remLst = arr[:i] + arr[i+1:]
        
        for p in permutations(remLst):
        
            l.append([m] + p)
            
    return l

**As complexidades são**:

O(1): Constante

O(log n): Logarítmica

O(n): Linear

O(n log n): Linearítmica

O(n^2): Quadrática

O(n^3): Cúbica

O(2^n): Exponencial

O(n!): Fatorial

**De acordo com o algoritmo escolhido por mim o nível maior de complexidade O(n!) representa um dos crescimentos mais rápidos em algoritmos, tornando-os adequados apenas para pequenas entradas. É essencial identificar esses casos e buscar algoritmos mais eficientes ou heurísticas que possam fornecer soluções aproximadas em tempo razoável para problemas de maior escala**. Um exemplo de diferença de complexidade é entre o Fatorial (algoritmo escolhido) e o Exponencial (O(2^n)). 

**O que difere o Fatorial para o Exponencial é a questão da velocidade de resposta, ou seja, enquanto O(n!) age de forma mais rápida e constante, o O(2^n) cresce mas de forma lenta tornando impraticável para valores de N ainda menores em comparação com O(n!)**.


**O código do problema em Rust**:


extern crate nalgebra as na; 

extern crate lp_modeler; 

use lp_modeler::dsl::*;

use lp_modeler::problem::Problem;

use lp_modeler::solvers::{Solver, CbcSolver};

#[derive(Debug)]

struct Produto {

    nome: String,
    
    data_entrega: String,
    
    materias_primas: Vec<(String, f64)>, 

#[derive(Debug)]

struct MateriaPrima {

    nome: String,
    
    custo: f64,
    
    tempo_entrega: i32,
    
    disponibilidade: f64,
    
}

fn main() {
   
    let produtos = vec![
    
        Produto {
        
            nome: String::from("Produto A"),
            
            data_entrega: String::from("2024-07-01"),
            
            materias_primas: vec![(String::from("Materia 1"), 10.0), (String::from("Materia 2"), 5.0)],
            
        },
        
        Produto {
        
            nome: String::from("Produto B"),
            
            data_entrega: String::from("2024-07-05"),
            
            materias_primas: vec![(String::from("Materia 1"), 15.0), (String::from("Materia 3"), 8.0)],
            
        },
        
    ];

    let materias_primas = vec![
    
        MateriaPrima {
        
            nome: String::from("Materia 1"),
            
            custo: 5.0,
            
            tempo_entrega: 3,
            
            disponibilidade: 100.0,
            
        },
        MateriaPrima {
        
            nome: String::from("Materia 2"),
            
            custo: 3.0,
            
            tempo_entrega: 2,
            
            disponibilidade: 50.0,
            
        },
        MateriaPrima {
        
            nome: String::from("Materia 3"),
            
            custo: 4.0,
            
            tempo_entrega: 4,
            
            disponibilidade: 70.0,
            
        },
    ];

  
    let mut problem = Problem::new("Planejamento de Produção");

  
    let mut vars = vec![];
    
    for materia in &materias_primas {
    
        let var = LpContinuous::new(&materia.nome);
        
        vars.push(var.clone());
        
        problem += (var.clone() * materia.custo).le(materia.disponibilidade);
        
    }

    for produto in &produtos {
    
        for (nome_materia, qtd_necessaria) in &produto.materias_primas {
        
            let var = vars.iter().find(|v| v.name == *nome_materia).unwrap();
            
            problem += var.clone().ge(*qtd_necessaria);
        }
    }

    let objective = vars.iter().fold(LpExpression::new(), |acc, var| acc + var.clone());
    
    problem += objective.minimize();

    let solver = CbcSolver::new();
    
    let solution = solver.run(&problem).unwrap();

    for (i, var) in vars.iter().enumerate() {
    
        println!("{}: {}", var.name, solution[var]);
    }
}


**Explicação do Código**:


Estrutura dos Dados: Definir as estruturas Produto e MateriaPrima para armazenar informações sobre produtos e matérias-primas.

Problema de Programação Linear: Utilizar a crate lp_modeler para definir e resolver um problema de programação linear.

Variáveis: Criar variáveis para representar a quantidade de cada matéria-prima a ser comprada.

Restrições: Adicionar restrições para garantir que a quantidade de matérias-primas compradas seja suficiente para a produção dos produtos e não exceda a disponibilidade.

Função Objetivo: Definir a função objetivo para minimizar o custo total das matérias-primas.

Solução: Utilizar o solver 'CbcSolver' para resolver o problema e imprimir a solução.








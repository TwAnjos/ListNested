# ListNested
Lista aninhada recursiva, criando populando e lendo uma lista de objetos que possuem uma lista de objetos infinitos

----
Default.cs

        private HashSet<Pessoa> listaDePessoas;
        private List<Pessoa> ListaLienar = new List<Pessoa>();
        private HashSet<Pessoa> listAninhada = new HashSet<Pessoa>();

        protected void Page_Load(object sender, EventArgs e)
        {
            carregaListaDePessoas();
            listAninhada = RetornaListRecursiva();
            CarregaAListaLienar(listAninhada);

            var tw = JsonConvert.SerializeObject(listAninhada);
            var tw3 = JsonConvert.SerializeObject(ListaLienar);

                    }

        private void CarregaAListaLienar(HashSet<Pessoa> listAninhada)
        {
            int grau = 0;
            foreach (var la in listAninhada)
            {
                addNewPessoa(la, grau);
                grau++;
                //addOsFilhos
                List<Pessoa> filhola = pegaOsFilhos(la.listFilhos, grau);

                grau = 0;
            }
        }

        private List<Pessoa> pegaOsFilhos(List<Pessoa> listFilhos, int grau)
        {
            List<Pessoa> newlistpessoa = new List<Pessoa>();

            foreach (var item in listFilhos)
            {
                addNewPessoa(item, grau);
                newlistpessoa.Add(item);

                grau++;
                pegaOsFilhos(item.listFilhos, grau);
                grau--;
            }
            return newlistpessoa;
        }


        private Pessoa addNewPessoa(Pessoa la, int grau)
        {
            var np = new Pessoa
            {
                id = la.id,
                idPai = la.idPai,
                grauParentesco = grau,
                listFilhos = null
            };

            ListaLienar.Add(np);

            return np;
        }

        private HashSet<Pessoa> RetornaListRecursiva()
        {
            var lp = listaDePessoas
                .Where(pessoa => pessoa.idPai == 0)   //Localiza a primeira pessoa sem pai.
                .Select(pai => new Pessoa     //Seleciona a pessoa
                {
                    id = pai.id,
                    idPai = pai.idPai,
                    listFilhos = ObterFuncoesFilhas(listaDePessoas, pai.id),//insere os filhos
                }).ToHashSet();

            return lp;
        }

        //Resumindo... o HashSet funciona como um List melhorado, se você não sabe o que é Lista não deveria estar aqui...
        private List<Pessoa> ObterFuncoesFilhas(HashSet<Pessoa> listFunction, int idDoPai)
        {
            var lt = listFunction.Where(pessoa => pessoa.idPai == idDoPai)    //localiza quem tem o mesmo idPai que o id do pai.
                .Select(filho => new Pessoa()
                {
                    id = filho.id,
                    idPai = filho.idPai,
                    listFilhos = ObterFuncoesFilhas(listFunction, filho.id) //Chama essa mesma função para verificar se este filho Tem outros filhos, etc, etc....
                }).ToList();

            return lt;
        }

        private void carregaListaDePessoas()
        {
            //populando a lista com vários objeto Pessoa
            listaDePessoas = new HashSet<Pessoa>();
            listaDePessoas.Add(new Pessoa() { id = 1, idPai = 0 });
            listaDePessoas.Add(new Pessoa() { id = 3, idPai = 1 });
            listaDePessoas.Add(new Pessoa() { id = 6, idPai = 1 });
            listaDePessoas.Add(new Pessoa() { id = 7, idPai = 6 });
            listaDePessoas.Add(new Pessoa() { id = 8, idPai = 6 });
            listaDePessoas.Add(new Pessoa() { id = 666, idPai = 8 });//filho maltido
            listaDePessoas.Add(new Pessoa() { id = 9, idPai = 6 });

            listaDePessoas.Add(new Pessoa() { id = 4, idPai = 0 });
            listaDePessoas.Add(new Pessoa() { id = 5, idPai = 4 });
            listaDePessoas.Add(new Pessoa() { id = 12, idPai = 4 });
            listaDePessoas.Add(new Pessoa() { id = 10, idPai = 5 });
            listaDePessoas.Add(new Pessoa() { id = 11, idPai = 5 });

            listaDePessoas.Add(new Pessoa() { id = 13, idPai = 1 });
        }

        public class Pessoa
        {
            public Pessoa()
            {
                listFilhos = new List<Pessoa>();
            }

            public int id { get; set; }
            public int idPai { get; set; }
            public int grauParentesco { get; set; }
            public List<Pessoa> listFilhos { get; set; }
        }
        

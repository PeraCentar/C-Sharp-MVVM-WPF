C-Sharp-MVVM-WPF
C # VPF MVVM applications
using System;
using System.Collections.Generic;
using System.Collections.ObjectModel;
using System.IO;
using System.Linq;
using System.Runtime.Serialization.Json;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Input;
using WPF2.Command;

namespace WPF2.ViewModel
{
    class AddStudentViewModel : ViewModelBase
    {

        private const string imeFajla = "podaci.json";

        public AddStudentViewModel()
        {
            Student = new Student();
            deserijalizacija();
        }

        private Student student;
        public Student Student
        {
            get
            {
                return student;
            }
            set
            {
                student = value;
                OnPropertyChanged("Student");
            }
        }

        private ObservableCollection<Student> listaStudenata;
        public ObservableCollection<Student> ListaStudenata
        {
            get
            {
                return listaStudenata;
            }
            set
            {
                listaStudenata = value;
                OnPropertyChanged("ListaStudenata");
            }
        }



        #region Commands
        private ICommand noviUnos;
        public ICommand NoviUnos
        {
            get
            {
                if (noviUnos == null)
                {
                    noviUnos = new RelayCommand(param => NoviUnosExec(), param => CanNoviUnosExec());
                }
                return noviUnos;
            }
        }

        private void NoviUnosExec()
        {
            Student = new Student();
        }
        private bool CanNoviUnosExec()
        {
            return true;
        }


        private ICommand ubaci;
        public ICommand Ubaci
        {
            get
            {
                if (ubaci == null)
                {
                    ubaci = new RelayCommand(param => UbaciExec(), param => CanUbaciExec());
                }
                return ubaci;
            }
        }

        private void UbaciExec()
        {
            if (postoji(Student.StudentId))
            {
                MessageBox.Show("Student sa ID: " + Student.StudentId + " vec postoji!", "Obavjestenje");
            }
            else
            {
                ListaStudenata.Add(Student);
                serijalizacija();
                deserijalizacija();
            }

        }

        private bool CanUbaciExec()
        {
            if (Student.StudentId <= 0 || Student.GodinaStudija <=0)
            {
                return false;
            }
            else
            {
                return true;
            }
        }

        private ICommand promeni;
        public ICommand Promeni
        {
            get
            {
                if (promeni == null)
                {
                    promeni = new RelayCommand(param => PromeniExec(), param => CanPromeniExec());
                }
                return promeni;
            }
        }

        private void PromeniExec()
        {
            serijalizacija();
            deserijalizacija();
            MessageBox.Show("Podaci promenjeni", "Obavjestenje");
        }
        private bool CanPromeniExec()
        {
            if (Student.StudentId == 0)
            {
                return false;
            }
            return true;
        }

        private ICommand obrisi;
        public ICommand Obrisi
        {
            get
            {
                if (obrisi == null)
                {
                    obrisi = new RelayCommand(param => ObrisiExec(), param => CanObrisiExec());
                }
                return obrisi;
            }
        }

        private void ObrisiExec()
        {
            ListaStudenata.Remove(Student);
            serijalizacija();
            deserijalizacija();

        }
        private bool CanObrisiExec()
        {
            if (Student.StudentId == 0)
            {
                return false;
            }
            return true;
        }

        private bool postoji(int id)
        {
            foreach (Student s in ListaStudenata)
            {
                if (s.StudentId == id)
                {
                    return true;
                }
            }
            return false;
        }

        public void serijalizacija()
        {
            FileStream fs = File.Create(imeFajla);
            DataContractJsonSerializer js = new DataContractJsonSerializer(typeof(ObservableCollection<Student>));
            js.WriteObject(fs, ListaStudenata);
            Student = new Student();
            fs.Close();
            fs.Dispose();
        }

        private void deserijalizacija()
        {
            FileStream fs = File.OpenRead(imeFajla);
            DataContractJsonSerializer js = new DataContractJsonSerializer(typeof(ObservableCollection<Student>));
            ListaStudenata = (ObservableCollection<Student>)js.ReadObject(fs);
            Student = new Student();
            fs.Close();
            fs.Dispose();
        }


        #endregion

    }
}

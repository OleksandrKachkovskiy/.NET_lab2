using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;
using System.Security.Cryptography;
using System.IO;

namespace MyLittleComparator
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }
        // Обираю каталоги та отримую шлях до кожного з них
        private void button1_Click(object sender, EventArgs e)
        {
            FolderBrowserDialog First_folder = new FolderBrowserDialog();
            First_folder.ShowDialog();
            textBox1.Text = First_folder.SelectedPath;
        }

        private void button2_Click(object sender, EventArgs e)
        {
            FolderBrowserDialog Second_folder = new FolderBrowserDialog();
            Second_folder.ShowDialog();
            textBox2.Text = Second_folder.SelectedPath;
        }

        int Button_click_counter = 0; // для того, щоб очистити контейнери перед записом нових даних

        private int Get_Files_Hash()
        {       // знаходжу хеш-код кожного файлу і записую в listBox'и. 
                // Я не використовував списки чи масиви для зберігання значень тому, 
                // що виводжу їх у listBox'и для візуалізаії і не хочу дублювати.
            try
            {            
                DirectoryInfo First_folder_path = new DirectoryInfo(textBox1.Text);
                FileInfo[] First_folder_files = First_folder_path.GetFiles();

                foreach (FileInfo file in First_folder_files)
                {
                    string File_path = file.FullName;
                    byte[] Hash_bytes;
                    using (var inputFileStream = File.Open(File_path, FileMode.Open))
                    {
                       var md5 = MD5.Create();
                       Hash_bytes = md5.ComputeHash(inputFileStream);
                       string result = BitConverter.ToString(Hash_bytes).Replace("-", String.Empty);
                       listBox1.Items.Add("Ім'я файлу: " + file + " Хеш-код файлу: " + result);
                    }   
                }

                DirectoryInfo Second_folder_path = new DirectoryInfo(textBox2.Text);
                FileInfo[] Second_folder_files = Second_folder_path.GetFiles();

                foreach (FileInfo file in Second_folder_files)
                {
                    string File_path = file.FullName;
                    byte[] Hash_bytes;
                    using (var inputFileStream = File.Open(File_path, FileMode.Open))
                    {
                        var md5 = MD5.Create();
                        Hash_bytes = md5.ComputeHash(inputFileStream);
                        string result = BitConverter.ToString(Hash_bytes).Replace("-", String.Empty);
                        listBox2.Items.Add("Ім'я файлу: " + file + " Хеш-код файлу: " + result);
                    }
                }

                Button_click_counter++; // рахую, скільки разів здійснив пошук, щоб очистити контейнери перед наступним
                // шукаю однакові файли
                foreach (var Listbox1_item in listBox1.Items)
                {
                    int Match_found = 0;
                    foreach (var Listbox2_item in listBox2.Items)
                    {
                        
                        if (Listbox1_item.Equals(Listbox2_item) == true)
                        {
                            listBox3.Items.Add(Listbox1_item);
                            Match_found++;
                        }
                    }

                    if (Match_found < 1)
                    {
                        listBox3.Items.Add("Однакових файлів не знайдено");
                        break;
                    }
                }
            }

            catch
            {
                MessageBox.Show("Перевірте можливіть доступу до каталогу та впевніться, що обрали два каталоги");
                textBox1.Clear();
                textBox2.Clear();
                listBox1.Items.Clear();
                listBox2.Items.Clear();
                listBox3.Items.Clear();
            }

            return Button_click_counter;
        }

        private void button3_Click(object sender, EventArgs e)
        {
            if (Button_click_counter > 0)
            {  // очищую контейнери перед наступним пошуком
                textBox1.Clear();
                textBox2.Clear();
                listBox1.Items.Clear();
                listBox2.Items.Clear();
                listBox3.Items.Clear();
                Button_click_counter = 0;
            }
            else
            {
                Get_Files_Hash();
            }

        }
    }
}

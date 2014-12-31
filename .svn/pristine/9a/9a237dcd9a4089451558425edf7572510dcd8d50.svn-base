using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Text;
using System.Windows.Forms;
using System.IO;
using iTextSharp.text;
using iTextSharp.text.pdf;
using iTextSharp.text.pdf.parser;

using System.Text.RegularExpressions;

namespace eBookManage
{
    public partial class Form1 : Form
    {
        string category, extension, group;
        int moveCount = 0, emptyCount = 0, addCateCount = 0, editCount = 0, deleteCount = 0;
        string logFileName, deleteFileName;
        public Form1()
        {
            InitializeComponent();
        }

        private void button1_Click(object sender, EventArgs e)
        {
            FolderBrowserDialog fbDialog = new FolderBrowserDialog();
            if (fbDialog.ShowDialog() == DialogResult.OK)
            {
                textBox1.Text = fbDialog.SelectedPath;
            }

        }

        private void button3_Click(object sender, EventArgs e)
        {
            FolderBrowserDialog fbDialog = new FolderBrowserDialog();
            if (fbDialog.ShowDialog() == DialogResult.OK)
            {
                textBox2.Text = fbDialog.SelectedPath;
            }
        }

        private void button2_Click(object sender, EventArgs e)
        {
            if (textBox1.Text == "")
            {
                MessageBox.Show("You have to choose source path!", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
                return;
            }

            if (textBox2.Text == "")
            {
                MessageBox.Show("You have to select destination path!", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
                return;
            }

            if (textBox1.Text.EndsWith(@"\"))
                textBox1.Text = textBox1.Text.Substring(0, textBox1.Text.Length - 1);


            if (textBox2.Text.EndsWith(@"\"))
                textBox2.Text = textBox2.Text.Substring(0, textBox2.Text.Length - 1);


            foreach (Control c in this.Controls)
                c.Enabled = false;

            category = System.Configuration.ConfigurationManager.OpenExeConfiguration(System.Reflection.Assembly.GetExecutingAssembly().Location).AppSettings.Settings["category"].Value;
            extension = System.Configuration.ConfigurationManager.OpenExeConfiguration(System.Reflection.Assembly.GetExecutingAssembly().Location).AppSettings.Settings["extension"].Value;
            logFileName = "log_moving_" + DateTime.Now.ToString("dd_MM_yyyy_HH_mm_ss") + ".txt";


            label5.Text = "Moving file...";
            moveCount = 0;
            MovingFiles(textBox1.Text);

            label5.Text = "Empting folder...";
            emptyCount = 0;
            DeleteEmptyFolder(textBox1.Text);

            label5.Text = "Finished!";
            label3.Text = "Moved: " + moveCount + " / Deleted empty folder: " + emptyCount;

            foreach (Control c in this.Controls)
                c.Enabled = true;
        }

        private void MovingFiles(string sourcePath)
        {
            string[] categories = category.Split(',');
            string[] extensions = extension.Split(',');

            string[] dirs = Directory.GetDirectories(sourcePath);
            foreach (string dir in dirs)
            {
                MovingFiles(dir);
            }

            string[] files = Directory.GetFiles(sourcePath);
            string fileName, fileExt;
            foreach (string file in files)
            {

                fileName = Path.GetFileNameWithoutExtension(file);
                fileExt = Path.GetExtension(file);

                label3.Text = file;
                Application.DoEvents();

                if(!checkBox6.Checked)
                    if (!CheckFileName(fileName, categories))
                        continue;

                if(!checkBox7.Checked)
                    if (!CheckExt(fileExt, extensions))
                        continue;

                int i = 1;
                while (File.Exists(textBox2.Text + @"\" + fileName + fileExt))
                {
                    fileName = fileName + "(" + i + ")";
                    i++;
                }
                File.Move(file, textBox2.Text + @"\" + fileName + fileExt);
                Log(logFileName, file + "\t" + textBox2.Text + @"\" + fileName + fileExt);
                moveCount++;
            }
        }

        private void DeleteEmptyFolder(string sourcePath)
        {
            
            string[] dirs = Directory.GetDirectories(sourcePath);

            foreach (string dir in dirs)
            {
                DeleteEmptyFolder(dir);
            }



            //dat lenh xoa sau lenh duyet thu muc: dam bao xoa xong con se xoa cha
            string[] files = Directory.GetFiles(sourcePath);
            if ((files.Length == 1) && (Path.GetFileName(files[0]).ToLower() == "thumbs.db"))
            {
				File.SetAttributes(files[0], FileAttributes.Archive);
                File.Delete(files[0]);
                files = Directory.GetFiles(sourcePath);
            }

            dirs = Directory.GetDirectories(sourcePath);

            if ((dirs.Length == 0) && (files.Length == 0))
            {
                if (textBox1.Text != sourcePath)//khac root folder
                {
                    Directory.Delete(sourcePath);
                    emptyCount++;
                }
            }

            label3.Text = sourcePath;
            Application.DoEvents();


        }

        private string RebuildName(string name)
        {
            name = Regex.Replace(name, @"[^a-zA-Z0-9 \s]", " ");
            return " " + name + " ";
        }

        private string MakeBeautyFileName(string name)
        {
            name = name.ToLower();

            string[] chars = "a,b,c,d,e,f,g,h,i,j,k,l,m,n,o,p,q,r,s,t,u,v,w,x,y,z".Split(',');
            for (int i = 0; i < chars.Length; i++)
            {
                name = name.Replace(" " + chars[i], " " + chars[i].ToUpper());
                name = name.Replace("(" + chars[i], "(" + chars[i].ToUpper());
                name = name.Replace("-" + chars[i], "-" + chars[i].ToUpper());
            }

            return name.Substring(0, 1).ToUpper() + name.Substring(1, name.Length - 1);
        }


        private bool CheckFileName(string fileName, string[] cates)
        {
            fileName = RebuildName(fileName).ToLower();
            foreach (string cate in cates)
            {
                if (fileName.IndexOf(" " + cate.ToLower().Trim() + " ") != -1)
                    return true;
            }
            return false;
        }

        private bool CheckExt(string fileName, string[] cates)
        {
            fileName = fileName.ToLower();
            foreach (string cate in cates)
                if (fileName.EndsWith("." + cate.ToLower().Trim())) return true;
            return false;
        }

        private bool CheckType(string fileName, string[] cates)
        {
            foreach (string cate in cates)
                if (fileName.ToLower().IndexOf(cate.ToLower().Trim()) != -1) return true;
            return false;
        }

        private void button4_Click(object sender, EventArgs e)
        {
            FolderBrowserDialog fbDialog = new FolderBrowserDialog();
            if (fbDialog.ShowDialog() == DialogResult.OK)
            {
                textBox3.Text = fbDialog.SelectedPath;
            }
        }

        private void button8_Click(object sender, EventArgs e)
        {
            if (textBox3.Text == "")
            {
                MessageBox.Show("You have to choose source path!", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
                return;
            }

            if (textBox3.Text.EndsWith(@"\"))
                textBox3.Text = textBox3.Text.Substring(0, textBox3.Text.Length - 1);

            foreach (Control c in this.Controls)
                c.Enabled = false;


            progressBar1.Minimum = 0;
            progressBar1.Maximum = 100;
            progressBar1.Value = 0;

            label8.Text = "0/0";

            logFileName = "log_replace_" + DateTime.Now.ToString("dd_MM_yyyy_HH_mm_ss") + ".txt";

            editCount = 0;
            ReplaceFiles(textBox3.Text);

            label8.Text = "Replaced files: " + editCount;

            progressBar1.Value = 100;

            foreach (Control c in this.Controls)
                c.Enabled = true;

        }

        private void ReplaceFiles(string path)
        {
            if (textBox3.Text == "")
            {
                MessageBox.Show("You have to select path!", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
                return;
            }
            if (textBox4.Text == "")
            {
                MessageBox.Show("You have to enter text to replace!", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
                return;
            }

            string replaceFrom = textBox4.Text.ToLower();
            string replaceBy = textBox5.Text;
            if (replaceBy == "") replaceBy = " ";//dam bao ko bao gio remove khoang trang

            string[] files = Directory.GetFiles(path);
            int total = files.Length;
            int count = 1;
            string fileName, newName, fileExt;

            foreach (string file in files)
            {
                fileName = Path.GetFileNameWithoutExtension(file);

                fileExt = Path.GetExtension(file);

                newName = fileName.Trim();

                if (checkBox4.Checked)
                    newName = MakeBeautyFileName(newName);

                if (checkBox1.Checked)
                    if (newName.ToLower().StartsWith(replaceFrom))
                        newName = replaceBy + newName.Substring(replaceFrom.Length);

                if (checkBox2.Checked)
                    if (newName.ToLower().EndsWith(replaceFrom))
                        newName = newName.Substring(0, newName.Length - replaceFrom.Length) + replaceBy;

                if (replaceFrom.Length > 1)
                {
                    //tranh tinh trang replacefrom la 1 pattern cua regular expression: ., ?...
                    if (checkBox3.Checked)
                        if (newName.ToLower().IndexOf(replaceFrom) != -1)
                            //newName = newName.Replace(replaceFrom, replaceBy);
                            newName = Regex.Replace(newName, replaceFrom, replaceBy, RegexOptions.IgnoreCase);

                    //word
                    if (checkBox5.Checked)
                        if (newName.ToLower().IndexOf(" " + replaceFrom + " ") != -1)
                            newName = Regex.Replace(newName, " " + replaceFrom + " ", " " + replaceBy + " ", RegexOptions.IgnoreCase);
                        else if (newName.ToLower().StartsWith(replaceFrom + " "))
                            newName = replaceBy + newName.Substring(replaceFrom.Length);
                        else if (newName.ToLower().EndsWith(" " + replaceFrom))
                            newName = newName.Substring(0, newName.Length - replaceFrom.Length) + replaceBy;
                        else if (newName.ToLower().IndexOf(" " + replaceFrom + ",") != -1)
                            newName = Regex.Replace(newName, " " + replaceFrom + ",", " " + replaceBy + ",", RegexOptions.IgnoreCase);
                        else if (newName.ToLower().StartsWith(replaceFrom + ","))
                            newName = replaceBy + newName.Substring(replaceFrom.Length);
                        else if (newName.ToLower() == replaceFrom)
                            newName = replaceBy;
                }
                else
                {
                    if (checkBox3.Checked)
                        if (replaceFrom == ".")//neu la . -> co version : vd: php 5.3
                        {
                            if(newName.StartsWith(".")) 
                                newName = replaceBy+ newName.Substring(1);

                            if (newName.EndsWith(".")) 
                                newName = newName.Substring(0, newName.Length - 1) + replaceBy;

                            for(int i=1;i< newName.Length-1;i++)
                                if (newName[i].ToString() == replaceFrom)
                                {
                                    string c1 = newName[i - 1].ToString();
                                    string c2 = newName[i + 1].ToString();
                                    if(((c1=="0")||
                                        (c1=="1")||
                                        (c1=="2")||
                                        (c1=="3")||
                                        (c1=="4")||
                                        (c1=="5")||
                                        (c1=="6")||
                                        (c1=="7")||
                                        (c1=="8")||
                                        (c1=="9")
                                        )&&
                                        ((c2 == "0") ||
                                        (c2 == "1") ||
                                        (c2 == "2") ||
                                        (c2 == "3") ||
                                        (c2 == "4") ||
                                        (c2 == "5") ||
                                        (c2 == "6") ||
                                        (c2 == "7") ||
                                        (c2 == "8") ||
                                        (c2 == "9")
                                        )
                                        )//neu la 1.2, 1.3, ...
                                        continue;
                                    newName = newName.Replace(c1 + "." + c2, c1 + replaceBy + c2);
                                }
                        }
                        else
                        {
                            if (newName.ToLower().IndexOf(replaceFrom) != -1)
                                newName = newName.Replace(replaceFrom, replaceBy);
                        }
                    //word
                    if (checkBox5.Checked)
                        if (newName.ToLower().IndexOf(" " + replaceFrom + " ") != -1)
                            newName = newName.Replace(" " + replaceFrom + " ", " " + replaceBy + " ");
                        else if (newName.ToLower().StartsWith(replaceFrom + " "))
                            newName = replaceBy + newName.Substring(replaceFrom.Length);
                        else if (newName.ToLower().EndsWith(" " + replaceFrom))
                            newName = newName.Substring(0, newName.Length - replaceFrom.Length) + replaceBy;
                        else if (newName.ToLower().IndexOf(" " + replaceFrom + ",") != -1)
                            newName = newName.Replace(" " + replaceFrom + ",", " " + replaceBy + ",");
                        else if (newName.ToLower().StartsWith(replaceFrom + ","))
                            newName = replaceBy + newName.Substring(replaceFrom.Length);
                        else if (newName.ToLower() == replaceFrom)
                            newName = replaceBy;
                }


                newName = newName.Trim();

                while (newName.IndexOf(" ,") != -1)
                    newName = newName.Replace(" ,", ",");

                while (newName.IndexOf("  ") != -1)
                    newName = newName.Replace("  ", " ");


                if (fileName.ToLower() != newName.ToLower())
                {
                    int i = 1;
                    while (File.Exists(textBox3.Text + @"\" + newName + fileExt))
                    {
                        newName = newName + "(" + i + ")";
                    }
                    File.Move(file, textBox3.Text + @"\" + newName.Trim() + fileExt);
                    Log(logFileName, file + "\t" + textBox3.Text + @"\" + newName.Trim() + fileExt);
                    editCount++;
                }
                else if (fileName != newName)//lower-upper...
                {
                    File.Move(file, file + "_bak");
                    File.Move(file + "_bak", textBox3.Text + @"\" + newName.Trim() + fileExt);
                    Log(logFileName, file + "\t" + textBox3.Text + @"\" + newName.Trim() + fileExt);
                    editCount++;
                }
                progressBar1.Value = (count * 100) / total;
                label8.Text = count + "/" + total;

                Application.DoEvents();

                count++;
            }
        }

        private void Log(string fileName, string msg)
        {
            if (!Directory.Exists(Environment.CurrentDirectory + @"\Log"))
                Directory.CreateDirectory(Environment.CurrentDirectory + @"\Log");

            FileStream fs = new FileStream(@"Log\" + fileName, FileMode.Append, FileAccess.Write);
            StreamWriter sw = new StreamWriter(fs);
            sw.WriteLine(msg);
            sw.Close();
            fs.Close();
        }

        private void textBox4_Click(object sender, EventArgs e)
        {
            textBox4.SelectAll();
        }

        private void textBox5_Click(object sender, EventArgs e)
        {
            textBox5.SelectAll();
        }

        private void button7_Click(object sender, EventArgs e)
        {
            if (textBox3.Text == "")
            {
                MessageBox.Show("You have to choose source path!", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
                return;
            }

            if (textBox3.Text.EndsWith(@"\"))
                textBox3.Text = textBox3.Text.Substring(0, textBox3.Text.Length - 1);

            foreach (Control c in this.Controls)
                c.Enabled = false;

            CheckDuplicate();

            label8.Text = "finished";

            foreach (Control c in this.Controls)
                c.Enabled = true;
        }

        private string[] BubleSort(string[] files)
        {
            FileInfo fi, fj;
            int i, j, newj;
            string s;
            i = files.Length;

            do
            {
                newj = 0;
                for (j = 1; j < i; j++)
                {
                    fj = new FileInfo(files[j]);
                    fi = new FileInfo(files[j - 1]);

                    if (fi.Length > fj.Length)// xap xep theo size
                    {
                        s = files[j];
                        files[j] = files[j - 1];
                        files[j - 1] = s;
                        newj = j;
                    }
                    else if ((fi.Length == fj.Length) && (files[j - 1].Length > files[j].Length))//cung size, xap xep theo ten file
                    {
                        s = files[j];
                        files[j] = files[j - 1];
                        files[j - 1] = s;
                        newj = j;
                    }
                }
                i = newj;

            }
            while (i > 0);

            return files;
        }
        private void CheckDuplicate()
        {
            string duplicateFolder = textBox3.Text + @"\duplicate";
            if (!Directory.Exists(duplicateFolder)) Directory.CreateDirectory(duplicateFolder);

            string[] files = Directory.GetFiles(textBox3.Text);

            //Optimizing bubble sort
            files = BubleSort(files);


            //check duplicate
            FileInfo fi, fj;
            int i, j;
            i = 0;
            while (i < files.Length - 1)
            {
                try
                {
                    //if (files[i].ToLower().IndexOf("essential php security") != -1)
                    //{
                    //    int x = 0;
                    //}
                    fi = new FileInfo(files[i]);
                    j = i + 1;
                    fj = new FileInfo(files[j]);

                    while (fi.Length == fj.Length)
                    {
                        label8.Text = files[j];
                        Application.DoEvents();

                        if (IsDuplicate(fi, fj))
                        {
                            //move file j
                            File.Move(files[j], duplicateFolder + @"\" + Path.GetFileName(files[j]));
                        }
                        j++;
                        fj = new FileInfo(files[j]);
                    }

                    label8.Text = files[i];
                    Application.DoEvents();
                }
                catch
                {
                    //file ko ton tai, over length
                }
                i++;
            }
        }

        private bool IsDuplicate(FileInfo f1, FileInfo f2)
        {
            using (FileStream fs1 = f1.OpenRead())
            using (FileStream fs2 = f2.OpenRead())
            {
                for (int i = 0; i < f1.Length; i++)
                {
                    Application.DoEvents();
                    if (fs1.ReadByte() != fs2.ReadByte())
                        return false;
                }
            }

            return true;
        }

        private bool IsExistsedDuplicate(FileInfo f1, FileInfo[] fileList, int start, int end)
        {
            if (end < start) return false;// ko tim thay file lap

            int pos = start + (end - start) / 2;

            FileInfo f2 = fileList[pos];

            if (f1.Length == f2.Length)
            {
                if (IsDuplicate(f1, f2)) return true;//neu lap -> true
                else if (IsExistsedDuplicate(f1, fileList, start, pos - 1)) return true;//neu ko lap, kiem tra nua truoc co lap thi tra ve true
                else if (IsExistsedDuplicate(f1, fileList, pos + 1, end)) return true;//kiem tra nua truoc ma ko co thi kiem tra nua sau
                else return false;//ko tim thay-> false;
            }
            else if (f1.Length < f2.Length)
            {
                return IsExistsedDuplicate(f1, fileList, start, pos - 1);
            }
            else return IsExistsedDuplicate(f1, fileList, pos + 1, end);
        }

        private void button5_Click(object sender, EventArgs e)
        {
            if (textBox3.Text == "")
            {
                MessageBox.Show("You have to choose source path!", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
                return;
            }

            if (textBox3.Text.EndsWith(@"\"))
                textBox3.Text = textBox3.Text.Substring(0, textBox3.Text.Length - 1);

            foreach (Control c in this.Controls)
                c.Enabled = false;

            progressBar1.Minimum = 0;
            progressBar1.Maximum = 100;
            progressBar1.Value = 0;

            label8.Text = "0/0";

            logFileName = "log_add_category_" + DateTime.Now.ToString("dd_MM_yyyy_HH_mm_ss") + ".txt";

            addCateCount = 0;

            AddCategory(textBox3.Text);

            label8.Text = "Added category: " + addCateCount;

            progressBar1.Value = 100;


            foreach (Control c in this.Controls)
                c.Enabled = true;
        }
        private void AddCategory(string path)
        {
            if (textBox3.Text == "")
            {
                MessageBox.Show("You have to select path!", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
                return;
            }

            group = System.Configuration.ConfigurationManager.OpenExeConfiguration(System.Reflection.Assembly.GetExecutingAssembly().Location).AppSettings.Settings["group"].Value;
            string[] groups = group.Split(';');

            string[] files = Directory.GetFiles(path);
            string fileName, fileExt, groupName;
            int count = 0;
            int total = files.Length;

            foreach (string file in files)
            {
                fileName = Path.GetFileNameWithoutExtension(file);

                progressBar1.Value = (count * 100) / total;
                label8.Text = count + "/" + total;

                Application.DoEvents();

                count++;

                if (fileName.StartsWith("[")) continue; // da co group roi

                fileExt = Path.GetExtension(file);
                groupName = GetGroup(fileName, groups);
                if (groupName != "")
                {
                    File.Move(file, path + @"\" + groupName + fileName + fileExt);
                    Log(logFileName, file + "\t" + path + @"\" + groupName + fileName + fileExt);
                    addCateCount++;
                }
            }

        }
        private string GetGroup(string fileName, string[] groups)
        {
            fileName = fileName.ToLower().Replace(",", " ").Replace(".", " ");
            string groupName;

            foreach (string group in groups)
            {
                string[] s = group.Split(':');
                groupName = s[0].ToUpper();
                string[] groupList = s[1].ToLower().Trim().Split(',');

                foreach (string l in groupList)
                {
                    if (fileName.StartsWith(l + " ")) return "[" + groupName + "] ";
                    else if (fileName.EndsWith(" " + l)) return "[" + groupName + "] ";
                    else if (fileName.IndexOf(" " + l + " ") != -1) return "[" + groupName + "] ";
                    else if (fileName == l) return "[" + groupName + "] ";
                }

            }
            return "";
        }

        private string GetCurrentGroup(string fileName, string[] groups)
        {
            fileName = fileName.ToLower().Replace(",", " ").Replace(".", " ");
            string groupName;

            foreach (string group in groups)
            {
                string[] s = group.Split(':');
                groupName = s[0].ToLower();
                if (fileName.StartsWith("[" + groupName + "]")) return "[" + groupName + "]";
            }
            return "";
        }

        private void button6_Click(object sender, EventArgs e)
        {
            if (textBox3.Text == "")
            {
                MessageBox.Show("You have to choose source path!", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
                return;
            }

            if (textBox3.Text.EndsWith(@"\"))
                textBox3.Text = textBox3.Text.Substring(0, textBox3.Text.Length - 1);
            foreach (Control c in this.Controls)
                c.Enabled = false;

            progressBar1.Minimum = 0;
            progressBar1.Maximum = 100;
            progressBar1.Value = 0;

            label8.Text = "0/0";

            logFileName = "log_remove_category_" + DateTime.Now.ToString("dd_MM_yyyy_HH_mm_ss") + ".txt";

            addCateCount = 0;

            RemoveCategory(textBox3.Text);

            label8.Text = "Removed category: " + addCateCount;

            progressBar1.Value = 100;


            foreach (Control c in this.Controls)
                c.Enabled = true;
        }

        private void RemoveCategory(string path)
        {
            if (textBox3.Text == "")
            {
                MessageBox.Show("You have to select path!", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
                return;
            }

            group = System.Configuration.ConfigurationManager.OpenExeConfiguration(System.Reflection.Assembly.GetExecutingAssembly().Location).AppSettings.Settings["group"].Value;

            string[] groups = group.Split(';');

            string[] files = Directory.GetFiles(path);
            string fileName, fileExt, groupName;
            int count = 0;
            int total = files.Length;

            foreach (string file in files)
            {
                fileName = Path.GetFileNameWithoutExtension(file);

                progressBar1.Value = (count * 100) / total;
                label8.Text = count + "/" + total;
                count++;

                Application.DoEvents();

                if (!fileName.StartsWith("[")) continue; // ko co group

                fileExt = Path.GetExtension(file);
                groupName = GetCurrentGroup(fileName, groups);
                if (groupName != "")
                {
                    File.Move(file, path + @"\" + fileName.Substring(groupName.Length).Trim() + fileExt);
                    Log(logFileName, file + "\t" + path + @"\" + fileName.Substring(groupName.Length).Trim() + fileExt);
                    addCateCount++;
                }
            }

        }

        private void button9_Click(object sender, EventArgs e)
        {
            OpenFileDialog oFileDialog = new OpenFileDialog();
            if (oFileDialog.ShowDialog() == DialogResult.OK)
            {
                textBox6.Text = oFileDialog.FileName;
            }
        }

        private void button10_Click(object sender, EventArgs e)
        {
            foreach (Control c in this.Controls)
                c.Enabled = false;


            progressBar2.Minimum = 0;
            progressBar2.Maximum = 100;
            progressBar2.Value = 0;

            label10.Text = "0/0";

            //logFileName = "log_replace_" + DateTime.Now.ToString("dd_MM_yyyy_HH_mm_ss") + ".txt";

            editCount = 0;
            RollBackFiles(textBox6.Text);

            label10.Text = "Roll back: " + editCount;

            progressBar2.Value = 100;

			File.SetAttributes(textBox6.Text, FileAttributes.Archive);
            File.Delete(textBox6.Text);

            foreach (Control c in this.Controls)
                c.Enabled = true;

        }

        private void RollBackFiles(string path)
        {

            string[] logs = new string[10000];
            int iLogCount = 0;
            string line;
            System.IO.StreamReader file = new System.IO.StreamReader(path);
            while ((line = file.ReadLine()) != null)
            {
                logs[iLogCount] = line;
                iLogCount++;
            }

            file.Close();

            char[] delimiters = new char[] { '\t' };
            for (int i = iLogCount; i > 0; i--)
            {
                string[] split = logs[i - 1].Split(delimiters);
                if (File.Exists(split[1]))
                {
                    editCount++;
                    File.Move(split[1], split[0]);
                    progressBar2.Value = (i * 100) / iLogCount;
                    label10.Text = i + "/" + iLogCount;
                }
            }
        }

        private void button11_Click(object sender, EventArgs e)
        {
            if (textBox1.Text == "")
            {
                MessageBox.Show("You have to choose source path!", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
                return;
            }

            if (textBox2.Text == "")
            {
                MessageBox.Show("You have to select destination path!", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
                return;
            }

            if (textBox1.Text.EndsWith(@"\"))
                textBox1.Text = textBox1.Text.Substring(0, textBox1.Text.Length - 1);


            if (textBox2.Text.EndsWith(@"\"))
                textBox2.Text = textBox2.Text.Substring(0, textBox2.Text.Length - 1);

            foreach (Control c in this.Controls)
                c.Enabled = false;

            category = System.Configuration.ConfigurationManager.OpenExeConfiguration(System.Reflection.Assembly.GetExecutingAssembly().Location).AppSettings.Settings["category"].Value;
            extension = System.Configuration.ConfigurationManager.OpenExeConfiguration(System.Reflection.Assembly.GetExecutingAssembly().Location).AppSettings.Settings["extension"].Value;
            logFileName = "log_unduplicate_moving_" + DateTime.Now.ToString("dd_MM_yyyy_HH_mm_ss") + ".txt";
            deleteFileName = "log_delete_" + DateTime.Now.ToString("dd_MM_yyyy_HH_mm_ss") + ".txt";


            label5.Text = "Moving/Deleting file...";
            moveCount = 0;
            deleteCount = 0;

            string[] files = Directory.GetFiles(textBox2.Text);

            files = BubleSort(files);
            FileInfo[] fis = new FileInfo[files.Length];
            for (int i = 0; i < files.Length; i++)
                fis[i] = new FileInfo(files[i]);

            UnDuplicateMoving(textBox1.Text, fis);

            label5.Text = "Empting folder...";
            emptyCount = 0;
            DeleteEmptyFolder(textBox1.Text);

            label5.Text = "Finished!";
            label3.Text = "Moved: " + moveCount + " / DeletedCount: " + deleteCount + " / Deleted empty folder: " + emptyCount;

            foreach (Control c in this.Controls)
                c.Enabled = true;

        }
        private void UnDuplicateMoving(string sourcePath, FileInfo[] fileInfor)
        {
            string[] categories = category.Split(',');
            string[] extensions = extension.Split(',');

            string[] dirs = Directory.GetDirectories(sourcePath);
            foreach (string dir in dirs)
            {
                UnDuplicateMoving(dir, fileInfor);
            }

            string[] files = Directory.GetFiles(sourcePath);
            string fileName, fileExt;
            FileInfo f1;
            foreach (string file in files)
            {
                try
                {
                    fileName = Path.GetFileNameWithoutExtension(file);
                    fileExt = Path.GetExtension(file);

                    label3.Text = file;
                    Application.DoEvents();

                    f1 = new FileInfo(file);
                    if (IsExistsedDuplicate(f1, fileInfor, 0, fileInfor.Length-1))
                    {
                        //xoa file goc da ton tai

                        File.SetAttributes(file, FileAttributes.Archive);
                        File.Delete(file);
                        deleteCount++;
                        Log(deleteFileName, file);
                        continue;
                    }

                    if (!checkBox6.Checked)
                        if (!CheckFileName(fileName, categories))
                            continue;

                    if (!checkBox7.Checked)
                        if (!CheckExt(fileExt, extensions))
                            continue;

                    int i = 1;
                    while (File.Exists(textBox2.Text + @"\" + fileName + fileExt))
                    {
                        fileName = fileName + "(" + i + ")";
                        i++;
                    }
                    File.Move(file, textBox2.Text + @"\" + fileName + fileExt);
                    Log(logFileName, file + "\t" + textBox2.Text + @"\" + fileName + fileExt);
                    moveCount++;
                }
                catch(Exception ex) {
                    Log(logFileName, "error:"+ex);
                }
            }
        }

        private void button13_Click(object sender, EventArgs e)
        {
            FolderBrowserDialog fbDialog = new FolderBrowserDialog();
            if (fbDialog.ShowDialog() == DialogResult.OK)
            {
                textBox7.Text = fbDialog.SelectedPath;
            }
        }

        private void button12_Click(object sender, EventArgs e)
        {
            foreach (Control c in this.Controls)
                c.Enabled = false;


            progressBar3.Minimum = 0;
            progressBar3.Maximum = 100;
            progressBar3.Value = 0;

            label11.Text = "0/0";

            logFileName = "log_pdf_info_" + DateTime.Now.ToString("dd_MM_yyyy_HH_mm_ss") + ".txt";
            deleteFileName = "log_pdf_bad_" + DateTime.Now.ToString("dd_MM_yyyy_HH_mm_ss") + ".txt";

            editCount = 0;



            ManipulateFiles(textBox7.Text);

            label11.Text = "Manipulated: " + editCount;

            progressBar3.Value = 100;

            foreach (Control c in this.Controls)
                c.Enabled = true;
        }
        private void ManipulateFiles(string path)
        {
            if (textBox7.Text == "")
            {
                MessageBox.Show("You have to select path!", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
                return;
            }

            string[] files = Directory.GetFiles(path);
            int i = 0, iCount = files.Length;
            foreach (string file in files)
            {
                Application.DoEvents();

                ParserPdfFile(file);
                editCount++;
                progressBar3.Value = (i * 100) / iCount;
                label11.Text = i + "/" + iCount;
                i++;
            }
        }
        private void ParserPdfFile(string file)
        {
            if (Path.GetExtension(file).ToLower() != ".pdf") return;

            try
            {
                //read content from pdf file
                PdfReader pdfReader = new PdfReader(file);

                /*string xml;
                byte[] b = pdfReader.Metadata;
                if (b != null)
                {
                    xml = new UTF8Encoding().GetString(b);
                }*/

                string text = "";
                try
                {
                    //doc 10 trang dau
                    int totalReadPage = pdfReader.NumberOfPages;
                    if (totalReadPage > 10) totalReadPage = 10;

                    for (int page = 1; page <= totalReadPage; page++)
                    {
                        ITextExtractionStrategy strategy = new SimpleTextExtractionStrategy();
                        string currentText = PdfTextExtractor.GetTextFromPage(pdfReader, page, strategy);

                        currentText = Encoding.UTF8.GetString(ASCIIEncoding.Convert(Encoding.Default, Encoding.UTF8, Encoding.Default.GetBytes(currentText)));
                        text += currentText;
                    }
                }
                catch 
                {
                    pdfReader.Close();

                    string fileName1 = Path.GetFileName(file);
                    string unOpenFolder = file.Substring(0, file.Length - fileName1.Length);
                    unOpenFolder += "Un Open/";

                    if (!Directory.Exists(unOpenFolder)) Directory.CreateDirectory(unOpenFolder);
                    File.Move(file, unOpenFolder + fileName1);

                    Log(deleteFileName, "[Un Open]: " + file);                
                }
                //end read content from pdf file

                if (text == "") return;

                text = text.Replace("\r", " ").Replace("\n", "_new_line_").Replace("\t", " ");

                string isbn = "", isbn10 = "", isbn13 = "", copyright = "", author = "", year = "";
                //Get ISBN, ...
                //isbn
                string pat = "( |_new_line_)isbn(?<isbn>.+?)( |_new_line_)";
                Regex r = new Regex(pat, RegexOptions.IgnoreCase);
                MatchCollection mc = r.Matches(text);
                foreach (Match m in mc)
                {
                    isbn = m.Groups["isbn"].Value.Replace("_new_line_", "").Replace(":", "").Trim();
                    if (isbn.StartsWith("-10")) { isbn = ""; break; }
                    if (isbn.StartsWith("-13")) { isbn = ""; break; }
                    break;
                }

                //isbn10
                pat = "( |_new_line_)isbn-10(?<isbn>.+?)( |_new_line_)";
                r = new Regex(pat, RegexOptions.IgnoreCase);
                mc = r.Matches(text);
                foreach (Match m in mc)
                {
                    isbn10 = m.Groups["isbn"].Value.Replace("_new_line_", "").Replace(":", "").Trim();
                    break;
                }

                //isbn13
                pat = "( |_new_line_)isbn-13(?<isbn>.+?)( |_new_line_)";
                r = new Regex(pat, RegexOptions.IgnoreCase);
                mc = r.Matches(text);
                foreach (Match m in mc)
                {
                    isbn13 = m.Groups["isbn"].Value.Replace("_new_line_", "").Replace(":", "").Trim();
                    break;
                }

                bool found = false;
                if (isbn.Length > 5) found = true;
                else if (isbn10.Length > 5) found = true;
                else if (isbn13.Length > 5) found = true;

                if (!found) {

                    pat = "( |_new_line_)isbn(?<isbn>.+?)_new_line_";
                    r = new Regex(pat, RegexOptions.IgnoreCase);
                    mc = r.Matches(text);
                    foreach (Match m in mc)
                    {
                        isbn = m.Groups["isbn"].Value.Replace("_new_line_", "").Replace(":", "").Trim();
                        if (isbn.StartsWith("-10")) { isbn = ""; break; }
                        if (isbn.StartsWith("-13")) { isbn = ""; break; }
                        break;
                    }

                    //isbn10
                    pat = "( |_new_line_)isbn-10(?<isbn>.+?)_new_line_";
                    r = new Regex(pat, RegexOptions.IgnoreCase);
                    mc = r.Matches(text);
                    foreach (Match m in mc)
                    {
                        isbn10 = m.Groups["isbn"].Value.Replace("_new_line_", "").Replace(":", "").Trim();
                        break;
                    }

                    //isbn13
                    pat = "( |_new_line_)isbn-13(?<isbn>.+?)_new_line_";
                    r = new Regex(pat, RegexOptions.IgnoreCase);
                    mc = r.Matches(text);
                    foreach (Match m in mc)
                    {
                        isbn13 = m.Groups["isbn"].Value.Replace("_new_line_", "").Replace(":", "").Trim();
                        break;
                    }
                }


                //copyright:
                //pat = "( |_new_line_)Copyright ©(?<year>.+?) by (?<author>.+?)_new_line_";
                pat = "( |_new_line_)(©|@)(?<copyright>.+?)_new_line_";
                r = new Regex(pat, RegexOptions.IgnoreCase);
                mc = r.Matches(text);
                foreach (Match m in mc)
                {
                    //year = m.Groups["year"].Value.Replace("_new_line_", "").Trim();
                    //author = m.Groups["author"].Value.Replace("_new_line_", "").Trim();
                    copyright = m.Groups["copyright"].Value.Replace("_new_line_", "").Trim();
                    //if (copyright.IndexOf(" ") != -1)
                    //{
                    //    year = copyright.Substring(0, copyright.IndexOf(" ")).Trim();
                    //    author = copyright.Substring(year.Length).Trim();
                    //    if (author.ToLower().StartsWith("by"))
                    //        author = author.Substring(2);
                    //}
                    //if (author == "")
                    //    author = copyright;
                    break;
                }

                //Log(logFileName, file + "\t" + isbn + "\t" + isbn10 + "\t" + isbn13 + "\t" + year + "\t" + author);
                Log(logFileName, file + "\t" + isbn + "\t" + isbn10 + "\t" + isbn13 + "\t" + copyright);
            }
            catch
            {
                string fileName = Path.GetFileName(file);
                string badFolder = file.Substring(0, file.Length - fileName.Length);
                badFolder += "bad/";

                if (!Directory.Exists(badFolder)) Directory.CreateDirectory(badFolder);
                File.Move(file, badFolder + fileName);
                Log(deleteFileName, file);
            }
            //end Get ISBN...
        }

    }
}
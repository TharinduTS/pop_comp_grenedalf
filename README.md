# Population comparison 2 - using bash first to make it more efficient

This starts by converting vcf files to tab files as in "https://github.com/TharinduTS/Comparing_populations"

also it uses a slightly altered python script from that github page (which is included at the end of this page)

# from tab files

First, find the column numbers of the individuals you want to compare with the following argument.
Run this in the directory with tab files
You can just edit samp_names and copy paste this on terminal
```
samp_name_1="F_Ghana_WZ_BJE4687_combined__sorted.bam"
samp_name_2="all_ROM19161_sorted.bam"
samp_name_3="all_calcaratus_sorted.bam"

mkdir ../"${samp_name_1}${samp_name_2}${samp_name_3}_selected_samples"

samp_1_col_no=$(head -1 combined_Chr10.g.vcf.gz_Chr10_GenotypedSNPs.vcf.gz_filtered.vcf.gz_selected.vcf.gz.tab | tr '\t' '\n' | cat -n | grep $samp_name_1 |cut -f 1)

samp_2_col_no=$(head -1 combined_Chr10.g.vcf.gz_Chr10_GenotypedSNPs.vcf.gz_filtered.vcf.gz_selected.vcf.gz.tab | tr '\t' '\n' | cat -n | grep $samp_name_2 |cut -f 1)

samp_3_col_no=$(head -1 combined_Chr10.g.vcf.gz_Chr10_GenotypedSNPs.vcf.gz_filtered.vcf.gz_selected.vcf.gz.tab | tr '\t' '\n' | cat -n | grep $samp_name_3 |cut -f 1)

echo $samp_1_col_no,$samp_2_col_no,$samp_3_col_no
```
# Include columns 1,2 and 3 as mandatory because they are needed info columns, and then add the numbers from previous command to the cut numbers and cut needed columns
```
for i in *vcf.gz.tab; do cut -f 1,2,3,4,21,22 $i > ../"${samp_name_1}${samp_name_2}${samp_name_3}_selected_samples"/${i}_selected_samples.tab;done
```
Then remove all ./. sites. Run this inside the directory with previously filtered tab files
```
mkdir no_empty
for i in *tab_selected_samples.tab; do grep -vwE "./." ${i} > no_empty/${i}_no_empty.tab;done

```

Copy this python script to the main directory for NOVOPlasty

compare_populations.py
```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
Created on Fri Sep  8 13:57:57 2023

@author: Tharindu
"""

import array
print('\n\n***************************\n')
print('Comparing population specific sites')
# set path to the current folder
print('Please check the working directory before running')
print('\n\n***************************\n')
import time
time.sleep(2.5)
import os

import inspect
import os

import sys

# to get the current working directory
directory = '/scratch/premacht/python_projects_2023/filtering_first'
os. chdir(directory)
print('working in', directory)
print('\n\n\n\n\n')


inputfile = sys.argv[1]


#sex = input('enter sex of each individual in correct order in capitol letters as M of F (eg. MMFF)\n')
#outputfile1 = input('enter the name you want for your output\n')
#proportion = input('enter the proportion here\n')

#print('Proportion is ', proportion, '\n')

print('printing first 10 lines of the file')


def open_file(inputfile):
    try:
        with open(inputfile, 'r') as file:
            content = file.read()
            # Do something with the file content (e.g., print it)
            return content
            print(content, 10)
    except FileNotFoundError:
        print(f"Error: File '{inputfile}' not found.")
    except Exception as e:
        print(f"An error occurred: {e}")


file_contents = open_file(inputfile)


#reading first line
tab_header=file_contents.split('\n', 1)[0]

# splitting 
samp_list=tab_header.split('\t')

print('your sample list is')
my_samp_list=samp_list[3:len(samp_list)]
print(my_samp_list)

file = open('my_samp_list.txt','w')
for item in my_samp_list:
	file.write(item+"\n")
file.close()

print('\n\n\n\n\n')

print('Your sample list was created as my_samp_list.txt. Please open with excel and add the corresponding genders as M or F in second column and population names in the third column***AND SAVE IT and enter the file name with full path')
file_with_sex_path = sys.argv[2]


data = [[] for _ in range(len(my_samp_list))]
with open(inputfile) as f:
    for line in f.readlines():
        elements = line.split()
        for x in range(len(my_samp_list)):
            data[x].append(elements[x])
            
samplevise_locs=data[3:len(data)]

print(samplevise_locs)

#saving this dataframe with all the data for later
import pandas as pd

full_data_df=pd.DataFrame(samplevise_locs)
full_data_df=full_data_df.T


print('\n\n***************************\n')
print('Removing all ./. sites')
print('\n\n***************************\n')
time.sleep(2.5)
full_data_df = full_data_df[full_data_df.ne('./.').all(1)]

#print(data[3])

# creating needed dataframes



file_with_sex=pd.read_table(file_with_sex_path,header=None)

file_with_sex.columns=['file','sex','pop']

print('Input was')

print(file_with_sex)

print('\n\n***************************\n')
print('Creating seperate dataframes for different populations')
print('\n\n***************************\n')
time.sleep(2.5)

# get a list of names
names=file_with_sex['pop'].unique().tolist()

print('\n\n')

print('available populations are\n')
print(names)
print('\n')

valid_response_list=names
pop_1_to_test=sys.argv[3]

while pop_1_to_test not in valid_response_list:
    pop_1_to_test=input('\n Please enter a valid population name\n')


pop_1_data= file_with_sex[file_with_sex['pop'] == pop_1_to_test]

print('using following individuals for pop1')
print('\n\n')
print(pop_1_data)


valid_response_list=names
pop_2_to_test=sys.argv[4]

while pop_2_to_test not in valid_response_list:
    pop_2_to_test=input('\n Please enter a valid population name\n')


pop_2_data= file_with_sex[file_with_sex['pop'] == pop_2_to_test]

print('using following individuals for pop2')
print('\n\n')
print(pop_2_data)


valid_response_list=names
pop_3_to_test=sys.argv[5]

while pop_3_to_test not in valid_response_list:
    pop_3_to_test=input('\n Please enter a valid population name\n')


pop_3_data= file_with_sex[file_with_sex['pop'] == pop_3_to_test]

print('using following individuals for pop3')
print('\n\n')
print(pop_3_data)



file_with_locs=pd.read_csv(inputfile, sep='\t',header=None)

print('\n\n***************************\n')
print('Removing all ./. sites')
print('\n\n***************************\n')
time.sleep(2.5)
file_with_locs = file_with_locs[file_with_locs.ne('./.').all(1)]



#switch rows and columns
file_with_locs_transposed=file_with_locs.T


#rename columns
file_with_locs_transposed.columns = ['C'+str(i) for i in range(0,file_with_locs_transposed.shape[1])]

#rename first column to file
file_with_locs_transposed = file_with_locs_transposed.rename(columns={'C0': 'file'})

print(file_with_locs_transposed)

print('\n Pop1')
pop_1_data_with_locs=pop_1_data.merge(file_with_locs_transposed, how = 'left', on = ['file'])
print(pop_1_data_with_locs)

print('\n Pop2')
pop_2_data_with_locs=pop_2_data.merge(file_with_locs_transposed, how = 'left', on = ['file'])
print(pop_2_data_with_locs)

print('\n Pop3')
pop_3_data_with_locs=pop_3_data.merge(file_with_locs_transposed, how = 'left', on = ['file'])
print(pop_3_data_with_locs)


# Get unique values for columns

#create an empty df first
filtered_df = pd.DataFrame(columns=['loc','Col','genotype','males','females'])

import numpy as np

print('\n\n***************************\n')
print('Removing heterozygous sites')
print('\n\n***************************\n')
time.sleep(2.5)




print('Removing heterozygous sites from pop_1\n\n')

pop_1_list_to_remove=[]

for i in range(3,pop_1_data_with_locs.shape[1]-1):
    #col_no=i-2
    col_name='C'+str(i-2)
    comparing_for_homo=pop_1_data_with_locs.loc[0][col_name].split('/')
    #comparing_for_homo.columns=['col1','col2']
    #compared=comparing_for_homo[0].equals(comparing_for_homo[1])
    print('\n')
    print(str(round(i/(pop_1_data_with_locs.shape[1]-1)*100,2))+' % completed')
    if str(comparing_for_homo[0])!=comparing_for_homo[1]:
        pop_1_list_to_remove.append(col_name)

print('removing this list of columns\n\n')
print(pop_1_list_to_remove)

pop_1_data_with_locs.drop(columns=pop_1_list_to_remove,inplace=True)

print(str(pop_1_data_with_locs.shape[1]-2)+' site/s out of total '+str(i)+' remaining after removing heterozygous sites\n')
print(pop_1_data_with_locs)


print('Removing heterozygous sites from pop_2\n\n')

pop_2_list_to_remove=[]

for i in range(3,pop_2_data_with_locs.shape[1]-1):
    #col_no=i-2
    col_name='C'+str(i-2)
    comparing_for_homo=pop_2_data_with_locs.loc[0][col_name].split('/')
    #comparing_for_homo.columns=['col1','col2']
    #compared=comparing_for_homo[0].equals(comparing_for_homo[1])
    print('\n')
    print(str(round(i/(pop_2_data_with_locs.shape[1]-1)*100,2))+' % completed')
    if str(comparing_for_homo[0])!=comparing_for_homo[1]:
        pop_2_list_to_remove.append(col_name)

print('removing this list of columns\n\n')
print(pop_2_list_to_remove)

pop_2_data_with_locs.drop(columns=pop_2_list_to_remove,inplace=True)

print(str(pop_2_data_with_locs.shape[1]-2)+' site/s out of total '+str(i)+' remaining after removing heterozygous sites\n')
print(pop_2_data_with_locs)


print('Removing heterozygous sites from pop_3\n\n')

pop_3_list_to_remove=[]

for i in range(3,pop_3_data_with_locs.shape[1]-1):
    #col_no=i-2
    col_name='C'+str(i-2)
    comparing_for_homo=pop_3_data_with_locs.loc[0][col_name].split('/')
    #comparing_for_homo.columns=['col1','col2']
    #compared=comparing_for_homo[0].equals(comparing_for_homo[1])
    print('\n')
    print(str(round(i/(pop_3_data_with_locs.shape[1]-1)*100,2))+' % completed')
    if str(comparing_for_homo[0])!=comparing_for_homo[1]:
        pop_3_list_to_remove.append(col_name)

print('removing this list of columns\n\n')
print(pop_3_list_to_remove)

pop_3_data_with_locs.drop(columns=pop_3_list_to_remove,inplace=True)

print(str(pop_3_data_with_locs.shape[1]-2)+' site/s out of total '+str(i)+' remaining after removing heterozygous sites\n')
print(pop_3_data_with_locs)



# Uncomment this section if you want to use multiple individuals for one population

print('\n\n***************************\n')
print('Removing heterozygous sites Further')
print('This comes in handy only when you are using more than one individual per population. This compares individuals within populatitons')
print('\n\n***************************\n')
time.sleep(2.5)



for col in pop_1_data_with_locs.columns:
    if len(pop_1_data_with_locs[col].unique()) != 1:
        pop_1_data_with_locs = pop_1_data_with_locs.drop(col,axis=1)



print(str(pop_1_data_with_locs.shape[1]-2)+' site/s out of total '+str(col)+' remaining after removing heterozygous sites - Population 1\n')
print(pop_1_data_with_locs)



for col in pop_2_data_with_locs.columns:
    if len(pop_2_data_with_locs[col].unique()) != 1:
        pop_2_data_with_locs = pop_2_data_with_locs.drop(col,axis=1)


print(str(pop_2_data_with_locs.shape[1]-2)+' site/s out of total '+str(col)+' remaining after removing heterozygous sites - Population 2\n')
print(pop_2_data_with_locs)

for col in pop_3_data_with_locs.columns:
    if len(pop_3_data_with_locs[col].unique()) != 1:
        pop_3_data_with_locs = pop_3_data_with_locs.drop(col,axis=1)



print(str(pop_3_data_with_locs.shape[1]-2)+' site/s out of total '+str(col)+' remaining after removing heterozygous sites - Population 3\n')
print(pop_3_data_with_locs)


#create empty df
all_similiar_df = pd.DataFrame(columns=['checking_col','location','pop1_geno','pop2_geno','pop3_geno'])
pop1_close_to_pop2_df = pd.DataFrame(columns=['checking_col','location','pop1_geno','pop2_geno','pop3_geno'])
pop1_close_to_pop3_df = pd.DataFrame(columns=['checking_col','location','pop1_geno','pop2_geno','pop3_geno'])
pop2_close_to_pop3_df = pd.DataFrame(columns=['checking_col','location','pop1_geno','pop2_geno','pop3_geno'])
other_df = pd.DataFrame(columns=['checking_col','location','pop1_geno','pop2_geno','pop3_geno'])


# iterating the columns
check_list_pop1= pop_1_data_with_locs.columns
check_list_pop2= pop_2_data_with_locs.columns
check_list_pop3= pop_3_data_with_locs.columns

# Python program to find the common elements
# in two lists
def common_member(a, b,c):
    a_set = set(a)
    b_set = set(b)
    c_set = set(c)
 
    if (a_set & b_set & c_set):
        return a_set & b_set & c_set
    else:
        print("No common elements")
   
# I should only use columns that are present in all 3 dataframes. Therefore removing others

check_list=common_member(check_list_pop1, check_list_pop2,check_list_pop3)
check_list=list(check_list)
check_list = [x for x in check_list if x.startswith('C')]

   

for i in range(1,len(check_list)):
    checking_column=check_list[i]
    current_location=file_with_locs_transposed[checking_column][1]
    pop1_genotype=pop_1_data_with_locs[checking_column]
    pop1_genotype=str(pop1_genotype[0])
    pop2_genotype=pop_2_data_with_locs[checking_column]
    pop2_genotype=str(pop2_genotype[0])
    pop3_genotype=pop_3_data_with_locs[checking_column]
    pop3_genotype=str(pop3_genotype[0])
    current_row=checking_column,current_location,pop1_genotype,pop2_genotype,pop3_genotype
    current_df=pd.DataFrame(current_row).T
    current_df.columns=['checking_col','location','pop1_geno','pop2_geno','pop3_geno']
    if pop1_genotype==pop2_genotype==pop3_genotype:
        all_similiar_df=pd.concat([all_similiar_df, current_df])
    elif pop1_genotype == pop2_genotype and pop2_genotype!=pop3_genotype:
        pop1_close_to_pop2_df=pd.concat([pop1_close_to_pop2_df, current_df])
    elif pop1_genotype != pop2_genotype and pop1_genotype==pop3_genotype:
        pop1_close_to_pop3_df=pd.concat([pop1_close_to_pop3_df, current_df])
    elif pop1_genotype != pop2_genotype and pop2_genotype==pop3_genotype:
        pop2_close_to_pop3_df=pd.concat([pop2_close_to_pop3_df, current_df])
    else:
        other_df=pd.concat([other_df, current_df])
    
#saving folder name
saving_dir="pop_comparisons_"+inputfile+"_"+pop_1_to_test+"_"+pop_2_to_test+"_"+pop_3_to_test+'/'
# exist or not.
if not os.path.exists(saving_dir):
      
    # if the demo_folder directory is not present 
    # then create it.
    os.makedirs(saving_dir)


#saving files    
saving_file_name_for_summary_1_close_to_2=saving_dir+pop_1_to_test+'_close_to_'+pop_2_to_test+'_sites_filtered_summary.tsv'
pop1_close_to_pop2_df.to_csv(saving_file_name_for_summary_1_close_to_2, sep="\t",index=False)

saving_file_name_for_summary_1_close_to_3=saving_dir+pop_1_to_test+'_close_to_'+pop_3_to_test+'_sites_filtered_summary.tsv'
pop1_close_to_pop3_df.to_csv(saving_file_name_for_summary_1_close_to_3, sep="\t",index=False)

saving_file_name_for_summary_2_close_to_3=saving_dir+pop_2_to_test+'_close_to_'+pop_3_to_test+'_sites_filtered_summary.tsv'
pop2_close_to_pop3_df.to_csv(saving_file_name_for_summary_2_close_to_3, sep="\t",index=False)

saving_file_name_for_summary_all_similiar=saving_dir+pop_1_to_test+'_equal_to_to_'+pop_2_to_test+'_and_'+pop_3_to_test+'_sites_filtered_summary.tsv'
all_similiar_df.to_csv(saving_file_name_for_summary_all_similiar, sep="\t",index=False)

saving_file_name_for_summary_all_different=saving_dir+pop_1_to_test+'_has_no_connection_to_'+pop_2_to_test+'_and_'+pop_3_to_test+'_sites_filtered_summary.tsv'
other_df.to_csv(saving_file_name_for_summary_all_different, sep="\t",index=False)

print('\n\n***************************\n')
print('5 comparison files were saved in pop_comparisons\n\n')
final_summary_name=saving_dir+'final_summary.txt'
f = open(final_summary_name, 'w')

f.write(pop_1_to_test+' was close to '+pop_2_to_test+' in '+str(len(pop1_close_to_pop2_df))+' site/s\n\n')
f.write(pop_1_to_test+' was close to '+pop_3_to_test+' in '+str(len(pop1_close_to_pop3_df))+' site/s\n\n')
f.write(pop_2_to_test+' was close to '+pop_3_to_test+' in '+str(len(pop2_close_to_pop3_df))+' site/s\n\n')
f.write(pop_1_to_test+' was equal to '+pop_2_to_test+' and '+pop_3_to_test+' in '+str(len(all_similiar_df))+' site/s\n\n')
f.write('no particular connection was found in rest')
f.write('\n\n***************************\n')
f.close()
```
Then run it like this
```bash
#!/bin/sh
#SBATCH --job-name=bwa_505
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=8:00:00
#SBATCH --mem=6gb
#SBATCH --output=comp_pop.%J.out
#SBATCH --error=comp_pop.%J.err
#SBATCH --account=def-ben
#SBATCH --array=1-10

#SBATCH --mail-user=premacht@mcmaster.ca
#SBATCH --mail-type=BEGIN
#SBATCH --mail-type=END
#SBATCH --mail-type=FAIL
#SBATCH --mail-type=REQUEUE
#SBATCH --mail-type=ALL

module load python/3.8.2
virtualenv --no-download ~/ENV
source ~/ENV/bin/activate
pip install --no-index --upgrade pip
pip install pandas --no-index

python3 compare_populations.py /scratch/premacht/python_projects_2023/filtering_first/F_Ghana_WZ_BJE4687_combined__sorted.bamall_ROM19161_sorted.bamall_calcaratus_sorted.bam_selected_samples/no_empty/combined_Chr${SLURM_ARRAY_TASK_ID}.g.vcf.gz_Chr1_GenotypedSNPs.vcf.gz_filtered.vcf.gz_selected.vcf.gz.tab_selected_samples.tab_no_empty.tab my_sample_list_with_sex_and_pop.txt Tropicalis Liberia Calcaratus
```

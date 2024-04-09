# More_efficient_pop_comp_new

First I combined all the tab files into one file removing the header
```
tail -q -n +2 *selected.vcf.gz.tab > combined_chrs.tab
```
Then I added headers to the file
```
echo -e "#CHROM\tPOS\tREF\tF_Ghana_WZ_BJE4687_combined__sorted.bam\tF_IvoryCoast_xen228_combined__sorted.bam\tF_Nigeria_EUA0331_combined__sorted.bam\tF_Nigeria_EUA0333_combined__sorted.bam\tF_SierraLeone_AMNH17272_combined__sorted.bam\tF_SierraLeone_AMNH17274_combined__sorted.bam\tJBL052_concatscafs_sorted.bam\tM_Ghana_WY_BJE4362_combined__sorted.bam\tM_Ghana_ZY_BJE4360_combined__sorted.bam\tM_Nigeria_EUA0334_combined__sorted.bam\tM_Nigeria_EUA0335_combined__sorted.bam\tM_SierraLeone_AMNH17271_combined__sorted.bam\tM_SierraLeone_AMNH17273_combined__sorted.bam\tXT10_WZ_no_adapt._sorted.bam\tXT11_WW_trim_no_adapt_scafconcat_sorted.bam\tXT1_ZY_no_adapt._sorted.bam\tXT7_WY_no_adapt__sorted.bam\tall_ROM19161_sorted.bam\tall_calcaratus_sorted.bam\tmello_GermSeq_sorted.bam" | cat - combined_chrs.tab >/tmp/out && mv /tmp/out combined_chrs.tab
```

created a smaller test file to test my codes first

```
head -500 no_empty_all_chrs.tab>testing.tab
```
Then run this on local computer if your files are not super big

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
import pandas as pd
import numpy as np
import csv
import ast
from numpy.random import randint


# to get the current working directory
directory = "/Users/Tharindu/Library/CloudStorage/OneDrive-McMasterUniversity/for_lab_and_research/Tharindu_on_Mac/lab/python_projects/population_comparison/More_efficient_pop_comp_new"
os. chdir(directory)
print('working in', directory)
print('\n\n\n\n\n')


inputfile = input('enter the file name with full path to your tab file with genotypes\n')


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
file_with_sex_path = input('enter the file name with full path to your tab file with sexes and pop names\n')


# data = [[] for _ in range(len(my_samp_list))]
# with open(inputfile) as f:
#     for line in f.readlines():
#         elements = line.split()
#         for x in range(len(my_samp_list)):
#             data[x].append(elements[x])
            
# samplevise_locs=data[3:len(data)]

# print(samplevise_locs)

# #saving this dataframe with all the data for later


# full_data_df=pd.DataFrame(samplevise_locs)
# full_data_df=full_data_df.T


# print('\n\n***************************\n')
# print('Removing all ./. sites')
# print('\n\n***************************\n')
# time.sleep(2.5)
# full_data_df = full_data_df[full_data_df.ne('./.').all(1)]

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


using_all_combos=input('\n Do you want to use all the population combinations? PLease enter Y or N\n')

if using_all_combos=='N':

    print('\n\n')
    
    print('available populations are\n')
    print(names)
    print('\n')
    
    valid_response_list=names
    pop_1_to_test=input('\n Please enter a valid population name as population 1 of comparison\n')
    
    while pop_1_to_test not in valid_response_list:
        pop_1_to_test=input('\n Please enter a valid population name\n')
    
    
    pop_1_data= file_with_sex[file_with_sex['pop'] == pop_1_to_test]
    
    print('using following individuals for pop1')
    print('\n\n')
    print(pop_1_data)
    
    
    valid_response_list=names
    pop_2_to_test=input('\n Please enter a valid population name as population 2 of comparison\n')
    
    while pop_2_to_test not in valid_response_list:
        pop_2_to_test=input('\n Please enter a valid population name\n')
    
    
    pop_2_data= file_with_sex[file_with_sex['pop'] == pop_2_to_test]
    
    print('using following individuals for pop2')
    print('\n\n')
    print(pop_2_data)
    
    
    valid_response_list=names
    pop_3_to_test=input('\n Please enter a valid population name as population 3 of comparison\n')
    
    while pop_3_to_test not in valid_response_list:
        pop_3_to_test=input('\n Please enter a valid population name\n')
    
    
    pop_3_data= file_with_sex[file_with_sex['pop'] == pop_3_to_test]
    
    print('using following individuals for pop3')
    print('\n\n')
    print(pop_3_data)
    
    p1=str(pop_1_data['file'].iloc[0])
    p2=str(pop_2_data['file'].iloc[0])
    p3=str(pop_3_data['file'].iloc[0])
    
    print('Reading input file')

    # read selected columns
    file_with_locs=pd.read_csv(inputfile, sep='\t',usecols=['#CHROM','POS',p1,p2,p3])

    #rename
    file_with_locs.columns=['chr','pos','p1','p2','p3']

    # print('saving temp csv file')
    # #remove headers
    # file_with_locs.to_csv('temp.tab', sep='\t', index=False)



    print('\n\n***************************\n')
    print('Removing empties')
    print('\n\n***************************\n')
    #time.sleep(2.5)
    file_with_locs = file_with_locs[file_with_locs.ne('./.').all(1)]


    # ***** pop1******
    
    print('\n\n***************************\n')
    print('Removing heterozygous sites')
    print('\n\n***************************\n')

    print('pop1')

    #extracting pop1
    pop1_all_sites=file_with_locs.iloc[:, [0,1,2]]

    #name
    #colname_pop1=str(pop1_all_sites.columns[2])

    #splitting it to 2 columns
    pop1_all_sites[['var1','var2']] = pop1_all_sites.p1.str.split('/',expand=True)

    # Delete heterozygous rows 
    pop1_homo_only = pop1_all_sites.drop(pop1_all_sites[pop1_all_sites['var1'] != pop1_all_sites['var2']].index)

    #select columns

    selected_pop1=pop1_homo_only.iloc[:, [0,1,3]]


    print('Removed '+str(pop1_all_sites.shape[0]-pop1_homo_only.shape[0])+' heterozygous sites')

    # ****************

    # ***** pop2******

    print('pop2')

    #extracting pop2
    pop2_all_sites=file_with_locs.iloc[:, [0,1,3]]

    #name
    #colname_pop2=str(pop2_all_sites.columns[2])

    #splitting it to 2 columns
    pop2_all_sites[['var1','var2']] = pop2_all_sites.p2.str.split('/',expand=True)

    # Delete heterozygous rows 
    pop2_homo_only = pop2_all_sites.drop(pop2_all_sites[pop2_all_sites['var1'] != pop2_all_sites['var2']].index)

    #select columns

    selected_pop2=pop2_homo_only.iloc[:, [0,1,3]]


    print('Removed '+str(pop2_all_sites.shape[0]-pop2_homo_only.shape[0])+' heterozygous sites')

    # ****************

    # ***** pop3******

    print('pop3')

    #extracting pop3
    pop3_all_sites=file_with_locs.iloc[:, [0,1,4]]

    #name
    #colname_pop3=str(pop3_all_sites.columns[2])

    #splitting it to 2 columns
    pop3_all_sites[['var1','var2']] = pop3_all_sites.p3.str.split('/',expand=True)

    # Delete heterozygous rows 
    pop3_homo_only = pop3_all_sites.drop(pop3_all_sites[pop3_all_sites['var1'] != pop3_all_sites['var2']].index)

    #select columns

    selected_pop3=pop3_homo_only.iloc[:, [0,1,3]]


    print('Removed '+str(pop3_all_sites.shape[0]-pop3_homo_only.shape[0])+' heterozygous sites')

    # ****************


    # Combine identifiers to make it easier to compare - pop1***

    #first I have to convert values to string
    pop1_homo_only['chr'] = pop1_homo_only['chr'].astype(str)
    pop1_homo_only['pos'] = pop1_homo_only['pos'].astype(str)

    #combine
    pop1_homo_only['Chr-pos'] = pop1_homo_only['chr'].astype(str) + pop1_homo_only['pos']

    #drop extras
    pop1_homo_only.drop(['chr', 'pos'], axis=1)

    # change column order

    pop1_homo_only = pop1_homo_only[['Chr-pos', 'p1']]


    # **********

    # Combine identifiers to make it easier to compare - pop2***

    #first I have to convert values to string
    pop2_homo_only['chr'] = pop2_homo_only['chr'].astype(str)
    pop2_homo_only['pos'] = pop2_homo_only['pos'].astype(str)

    #combine
    pop2_homo_only['Chr-pos'] = pop2_homo_only['chr'].astype(str) + pop2_homo_only['pos']

    #drop extras
    pop2_homo_only.drop(['chr', 'pos'], axis=1)

    # change column order

    pop2_homo_only = pop2_homo_only[['Chr-pos', 'p2']]


    # **********

    # Combine identifiers to make it easier to compare - pop3***

    #first I have to convert values to string
    pop3_homo_only['chr'] = pop3_homo_only['chr'].astype(str)
    pop3_homo_only['pos'] = pop3_homo_only['pos'].astype(str)

    #combine
    pop3_homo_only['Chr-pos'] = pop3_homo_only['chr'].astype(str) + pop3_homo_only['pos']

    #drop extras
    pop3_homo_only.drop(['chr', 'pos'], axis=1)

    # change column order

    pop3_homo_only = pop3_homo_only[['Chr-pos', 'p3']]


    # **********

    final_dataset=pd.concat([pop1_homo_only.set_index('Chr-pos'),pop2_homo_only.set_index('Chr-pos')], axis=1, join='inner').reset_index()

    final_dataset=pd.concat([final_dataset.set_index('Chr-pos'),pop3_homo_only.set_index('Chr-pos')], axis=1, join='inner').reset_index()
    # ******

    #uncomment this if you wanna save csv to check manually

    #final_dataset.to_csv('homo_inalized_data.tsv', sep="\t")

    #count similarities

    #first I have to convert and remove NAa

    print('\n*********************\n')
    print('Counting similarities and differences.....\n This may take a while \n Please wait...')
    print('\n*********************\n')

    final_dataset= final_dataset.replace('NaN', np.nan)

    all_equal=final_dataset.apply(lambda x: x.p1 == x.p2 == x.p3, axis = 1).value_counts().get(True, 0)



    # for readable copy*******

    # all_equal_db=final_dataset.apply(lambda x: x.p1 == x.p2 == x.p3, axis = 1)

    # all_equal_db.to_csv('all_equal_db.tsv', sep="\t")
    # ************




    a_eq_b_not_c=final_dataset.apply(lambda x: x.p1 == x.p2 != x.p3, axis = 1).value_counts().get(True, 0)


    # for readable copy*******

    # a_eq_b_not_c_db=final_dataset.apply(lambda x: x.p1 == x.p2 != x.p3, axis = 1)

    # a_eq_b_not_c_db.to_csv('a_eq_b_not_c_db.tsv', sep="\t")

    # ************


    a_eq_c_not_b=final_dataset.apply(lambda x: x.p1 == x.p3 != x.p2, axis = 1).value_counts().get(True, 0)

    # for readable copy*******

    # a_eq_c_not_b_db=final_dataset.apply(lambda x: x.p1 == x.p3 != x.p2, axis = 1)

    # a_eq_c_not_b_db.to_csv("a_eq_c_not_b_db.tsv", sep="\t")
    # ************





    b_eq_c_not_a=final_dataset.apply(lambda x: x.p2 == x.p3 != x.p1, axis = 1).value_counts().get(True, 0)

    # for readable copy*******
    # b_eq_c_not_a_db=final_dataset.apply(lambda x: x.p2 == x.p3 != x.p1, axis = 1)

    # b_eq_c_not_a_db.to_csv('b_eq_c_not_a_db.tsv',sep='\t')

    # *******************

    print("\n"+"all equal in "+str(all_equal)+"\n\n"+"a = b in "+str(a_eq_b_not_c)+'\n'+'a = c in '+str(a_eq_c_not_b)+'\n'+'b = c in '+str(b_eq_c_not_a))

else:


    # get all the possible populatiopn combinations
    
    import itertools
    pop_lists=[my_samp_list,my_samp_list,my_samp_list]
    
    
    #get all combinations
    all_pop_combinations=list(itertools.product(*pop_lists))
    
    #remove duplicates
    combination_list=list({*map(tuple, map(sorted, all_pop_combinations))})
    
    # Create df to store data
    summary_df = pd.DataFrame(columns=['p1','p2','p3','all equal','a=b','a=c','b=c'])
    
    for combo in range(0,len(combination_list)) :
        
    
        p1=combination_list[combo][0]
        p2=combination_list[combo][1]
        p3=combination_list[combo][2]
        
        if p1!=p2 and p1!=p3 and p2!=p3:
        
            #avoid using same individual as different pops
            
            print('calculating similarities and differences for '+str(len(combination_list))+ ' combinations. \n\n *****This might take hours depending on the number of populations******')
            print(p1,p2,p3)
    
    
            print('Reading input file')
            
            # read selected columns
            file_with_locs=pd.read_csv(inputfile, sep='\t',usecols=['#CHROM','POS',p1,p2,p3])
            
            #rename
            file_with_locs.columns=['chr','pos','p1','p2','p3']
            
            # print('saving temp csv file')
            # #remove headers
            # file_with_locs.to_csv('temp.tab', sep='\t', index=False)
            
            
            
            print('\n\n***************************\n')
            print('Removing empties')
            print('\n\n***************************\n')
            time.sleep(2.5)
            file_with_locs = file_with_locs[file_with_locs.ne('./.').all(1)]
            
            
            # ***** pop1******
            
            print('pop1')
            
            #extracting pop1
            pop1_all_sites=file_with_locs.iloc[:, [0,1,2]]
            
            #name
            #colname_pop1=str(pop1_all_sites.columns[2])
            
            #splitting it to 2 columns
            pop1_all_sites[['var1','var2']] = pop1_all_sites.p1.str.split('/',expand=True)
            
            # Delete heterozygous rows 
            pop1_homo_only = pop1_all_sites.drop(pop1_all_sites[pop1_all_sites['var1'] != pop1_all_sites['var2']].index)
            
            #select columns
            
            selected_pop1=pop1_homo_only.iloc[:, [0,1,3]]
            
            
            print('Removed '+str(pop1_all_sites.shape[0]-pop1_homo_only.shape[0])+' heterozygous sites')
            
            # ****************
            
            # ***** pop2******
            
            print('pop2')
            
            #extracting pop2
            pop2_all_sites=file_with_locs.iloc[:, [0,1,3]]
            
            #name
            #colname_pop2=str(pop2_all_sites.columns[2])
            
            #splitting it to 2 columns
            pop2_all_sites[['var1','var2']] = pop2_all_sites.p2.str.split('/',expand=True)
            
            # Delete heterozygous rows 
            pop2_homo_only = pop2_all_sites.drop(pop2_all_sites[pop2_all_sites['var1'] != pop2_all_sites['var2']].index)
            
            #select columns
            
            selected_pop2=pop2_homo_only.iloc[:, [0,1,3]]
            
            
            print('Removed '+str(pop2_all_sites.shape[0]-pop2_homo_only.shape[0])+' heterozygous sites')
            
            # ****************
            
            # ***** pop3******
            
            print('pop3')
            
            #extracting pop3
            pop3_all_sites=file_with_locs.iloc[:, [0,1,4]]
            
            #name
            #colname_pop3=str(pop3_all_sites.columns[2])
            
            #splitting it to 2 columns
            pop3_all_sites[['var1','var2']] = pop3_all_sites.p3.str.split('/',expand=True)
            
            # Delete heterozygous rows 
            pop3_homo_only = pop3_all_sites.drop(pop3_all_sites[pop3_all_sites['var1'] != pop3_all_sites['var2']].index)
            
            #select columns
            
            selected_pop3=pop3_homo_only.iloc[:, [0,1,3]]
            
            
            print('Removed '+str(pop3_all_sites.shape[0]-pop3_homo_only.shape[0])+' heterozygous sites')
            
            # ****************
            
            
            # Combine identifiers to make it easier to compare - pop1***
            
            #first I have to convert values to string
            pop1_homo_only['chr'] = pop1_homo_only['chr'].astype(str)
            pop1_homo_only['pos'] = pop1_homo_only['pos'].astype(str)
            
            #combine
            pop1_homo_only['Chr-pos'] = pop1_homo_only['chr'].astype(str) + pop1_homo_only['pos']
            
            #drop extras
            pop1_homo_only.drop(['chr', 'pos'], axis=1)
            
            # change column order
            
            pop1_homo_only = pop1_homo_only[['Chr-pos', 'p1']]
            
            
            # **********
            
            # Combine identifiers to make it easier to compare - pop2***
            
            #first I have to convert values to string
            pop2_homo_only['chr'] = pop2_homo_only['chr'].astype(str)
            pop2_homo_only['pos'] = pop2_homo_only['pos'].astype(str)
            
            #combine
            pop2_homo_only['Chr-pos'] = pop2_homo_only['chr'].astype(str) + pop2_homo_only['pos']
            
            #drop extras
            pop2_homo_only.drop(['chr', 'pos'], axis=1)
            
            # change column order
            
            pop2_homo_only = pop2_homo_only[['Chr-pos', 'p2']]
            
            
            # **********
            
            # Combine identifiers to make it easier to compare - pop3***
            
            #first I have to convert values to string
            pop3_homo_only['chr'] = pop3_homo_only['chr'].astype(str)
            pop3_homo_only['pos'] = pop3_homo_only['pos'].astype(str)
            
            #combine
            pop3_homo_only['Chr-pos'] = pop3_homo_only['chr'].astype(str) + pop3_homo_only['pos']
            
            #drop extras
            pop3_homo_only.drop(['chr', 'pos'], axis=1)
            
            # change column order
            
            pop3_homo_only = pop3_homo_only[['Chr-pos', 'p3']]
            
            
            # **********
            
            final_dataset=pd.concat([pop1_homo_only.set_index('Chr-pos'),pop2_homo_only.set_index('Chr-pos')], axis=1, join='inner').reset_index()
            
            final_dataset=pd.concat([final_dataset.set_index('Chr-pos'),pop3_homo_only.set_index('Chr-pos')], axis=1, join='inner').reset_index()
            # ******
            
            #uncomment this if you wanna save csv to check manually
            
            #final_dataset.to_csv('homo_inalized_data.tsv', sep="\t")
            
            #count similarities
            
            #first I have to convert and remove NAa
            
            print('\n*********************\n')
            print('Counting similarities and differences.....\n This may take a while \n Please wait...')
            print('\n*********************\n')
            
            final_dataset= final_dataset.replace('NaN', np.nan)
            
            all_equal=final_dataset.apply(lambda x: x.p1 == x.p2 == x.p3, axis = 1).value_counts().get(True, 0)
            
            
            
            # for readable copy*******
            
            # all_equal_db=final_dataset.apply(lambda x: x.p1 == x.p2 == x.p3, axis = 1)
            
            # all_equal_db.to_csv('all_equal_db.tsv', sep="\t")
            # ************
            
            
            
            
            a_eq_b_not_c=final_dataset.apply(lambda x: x.p1 == x.p2 != x.p3, axis = 1).value_counts().get(True, 0)
            
            
            # for readable copy*******
            
            # a_eq_b_not_c_db=final_dataset.apply(lambda x: x.p1 == x.p2 != x.p3, axis = 1)
            
            # a_eq_b_not_c_db.to_csv('a_eq_b_not_c_db.tsv', sep="\t")
            
            # ************
            
            
            a_eq_c_not_b=final_dataset.apply(lambda x: x.p1 == x.p3 != x.p2, axis = 1).value_counts().get(True, 0)
            
            # for readable copy*******
            
            # a_eq_c_not_b_db=final_dataset.apply(lambda x: x.p1 == x.p3 != x.p2, axis = 1)
            
            # a_eq_c_not_b_db.to_csv("a_eq_c_not_b_db.tsv", sep="\t")
            # ************
            
            
            
            
            
            b_eq_c_not_a=final_dataset.apply(lambda x: x.p2 == x.p3 != x.p1, axis = 1).value_counts().get(True, 0)
            
            # for readable copy*******
            # b_eq_c_not_a_db=final_dataset.apply(lambda x: x.p2 == x.p3 != x.p1, axis = 1)
            
            # b_eq_c_not_a_db.to_csv('b_eq_c_not_a_db.tsv',sep='\t')
            
            # *******************
            
            print("\n"+"all equal in "+str(all_equal)+"\n\n"+"a = b in "+str(a_eq_b_not_c)+'\n'+'a = c in '+str(a_eq_c_not_b)+'\n'+'b = c in '+str(b_eq_c_not_a))
            
            to_write=p1,p2,p3,all_equal,a_eq_b_not_c,a_eq_c_not_b,b_eq_c_not_a
            
            summary_df.loc[len(summary_df.index)] = to_write
            
            # saving as tsv file 
            summary_df.to_csv('full_summary.tsv', sep="\t",index=False)
```

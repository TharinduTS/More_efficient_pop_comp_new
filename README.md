# More_efficient_pop_comp_new

First I combined all the tab files into one file removing the header
(after converting vcf to tab)
```
tail -q -n +2 *selected.vcf.gz.tab > combined_chrs.tab
```
Then I added headers to the file
```
echo -e "#CHROM\tPOS\tREF\tF_Ghana_WZ_BJE4687_combined__sorted.bam\tF_IvoryCoast_xen228_combined__sorted.bam\tF_Nigeria_EUA0331_combined__sorted.bam\tF_Nigeria_EUA0333_combined__sorted.bam\tF_SierraLeone_AMNH17272_combined__sorted.bam\tF_SierraLeone_AMNH17274_combined__sorted.bam\tJBL052_concatscafs_sorted.bam\tM_Ghana_WY_BJE4362_combined__sorted.bam\tM_Ghana_ZY_BJE4360_combined__sorted.bam\tM_Nigeria_EUA0334_combined__sorted.bam\tM_Nigeria_EUA0335_combined__sorted.bam\tM_SierraLeone_AMNH17271_combined__sorted.bam\tM_SierraLeone_AMNH17273_combined__sorted.bam\tXT10_WZ_no_adapt._sorted.bam\tXT11_WW_trim_no_adapt_scafconcat_sorted.bam\tXT1_ZY_no_adapt._sorted.bam\tXT7_WY_no_adapt__sorted.bam\tall_ROM19161_sorted.bam\tall_calcaratus_sorted.bam\tmello_GermSeq_sorted.bam" | cat - combined_chrs.tab >/tmp/out && mv /tmp/out combined_chrs.tab
```

created a smaller test file to test my codes first

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
from itertools import chain
import sys


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
    # rearrange populations to make sure they are in the proper order
    file_with_locs = file_with_locs.reindex(['#CHROM','POS',p1,p2,p3], axis=1)

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
    file_with_locs = file_with_locs[file_with_locs.ne('*').all(1)]


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
    
    # ask for population lists to use as pop1 , 2 and 3
    
    s_list_1=input('\n Please enter the file name with first population list\n')
    s_list_2=input('\n Please enter the file name with second population list\n')
    s_list_3=input('\n Please enter the file name with third population list\n')
    
    
    file1 = open(s_list_1, "r")
    list1=list(csv.reader(file1, delimiter="\t"))
    list1=list(chain.from_iterable(list1))
    
    file2 = open(s_list_2, "r")
    list2=list(csv.reader(file2, delimiter="\t"))
    list2=list(chain.from_iterable(list2))
    
    
    file3 = open(s_list_3, "r")
    list3=list(csv.reader(file3, delimiter="\t"))
    list3=list(chain.from_iterable(list3))
    
    pop_lists=[list1,list2,list3]
    
    #pop_lists=[my_samp_list,my_samp_list,my_samp_list]
    
    
    #get all combinations
    all_pop_combinations=list(itertools.product(*pop_lists))
    
    
    #remove duplicates
    combination_list=list({*map(tuple, map(sorted, all_pop_combinations))})

    
    #convert list to df
    combination_list=pd.DataFrame(combination_list)
    
    #drop cols rows with equal vals
    combination_list = combination_list.drop(combination_list[combination_list[0] == combination_list[1]].index)
    
    print('Saving a list of all possible combinations')
    combination_list.to_csv('combination_list.tsv',sep='\t',index=False)
    
    combination_file_desicion=input('Do you want to run this for all possible combinations (Y/N)?')
    if combination_file_desicion=='Y':
        combination_file='combination_list.tsv'
    else:
        combination_file=input('Please enter the customized combination file name')
    
    
    
    combination_list=pd.read_table(combination_file)
    combination_list.columns=['p1','p2','p3']
        
    # Create df to store data
    summary_df = pd.DataFrame(columns=['p1','p2','p3','all equal','a=b','a=c','b=c'])
    
    for combo in range(0,len(combination_list)) :
        
    
        p1=combination_list['p1'][combo]
        p2=combination_list['p2'][combo]
        p3=combination_list['p3'][combo]
        
        if p1!=p2 and p1!=p3 and p2!=p3:
            
            print('Running iteration '+str(combo)+' out of total'+str(len(combination_list)))
        
            #avoid using same individual as different pops
            
            print('calculating similarities and differences for '+str(len(combination_list))+ ' combinations. \n\n *****This might take hours depending on the number of populations******')
            print(p1,p2,p3)
    
    
            print('Reading input file')
            
            # read selected columns
            file_with_locs=pd.read_csv(inputfile, sep='\t',usecols=['#CHROM','POS',p1,p2,p3])
            # rearrange populations to make sure they are in the proper order
            file_with_locs = file_with_locs.reindex(['#CHROM','POS',p1,p2,p3], axis=1)
            
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
            file_with_locs = file_with_locs[file_with_locs.ne('*').all(1)]
            
            
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
            summary_df.to_csv('full_summary_testing.tsv', sep="\t",index=False)
```

# My files were huge and had hudreds of comparisons to do

# for that, I can either use the python script below to run multiple parellel jobs on my computer with the following command (this is going to open a new window for each new job)

```bash
for i in {40..41} ;do osascript -e 'tell application "Terminal" to do script "cd /Users/Tharindu/Library/CloudStorage/OneDrive-McMasterUniversity/for_lab_and_research/Tharindu_on_Mac/lab/python_projects/population_comparison/More_efficient_pop_comp_new ;python3 compare_populations_multiple_submissions.sh ./testing.tab  my_samp_list_with_sex_and_pop.txt Y N ./splitted_combination_lists/combination'${i}' outputs/sample_summary_output'${i}'.txt s_list_1.txt s_list_2.txt s_list_3.txt"'; done

```
# or follow following steps on computecanada

So I splitted my job into a bunch of smaller jobs on computecanada

For that,

1.First I used a sample file to come up with the combination_list with all the possible combinations (running previos script on local machine for a smaller file

2. Then I splitted that file into a bunch of smaller files like this
```
split --numeric-suffixes=1 -l 6 combination_list.tsv "combination"
```
Then I had to add column nnames to these combinations to keep it consistent
```
for i in combination*; do echo -e "0\t1\t2" | cat - ${i} >/tmp/out && mv /tmp/out ${i} ;done
```

# ** For some reason, my script had missed a few population combinations. So I added them to the last set of comparisons, manually. ********
# I had to amnually correct some file names because some arrays use 01,02 etc while others use 1,2. Please check this if you run into any issues

********and remove extra title from combination 1*****

and did the same to the combined chrs file so it is more efficient
```
split --numeric-suffixes=1 -l 6 combined_chrs.tab "combined_chrs_part"
```
and added headers to all of them
```
for i in combined_chrs_part*; do echo -e "#CHROM\tPOS\tREF\tF_Ghana_WZ_BJE4687_combined__sorted.bam\tF_IvoryCoast_xen228_combined__sorted.bam\tF_Nigeria_EUA0331_combined__sorted.bam\tF_Nigeria_EUA0333_combined__sorted.bam\tF_SierraLeone_AMNH17272_combined__sorted.bam\tF_SierraLeone_AMNH17274_combined__sorted.bam\tJBL052_concatscafs_sorted.bam\tM_Ghana_WY_BJE4362_combined__sorted.bam\tM_Ghana_ZY_BJE4360_combined__sorted.bam\tM_Nigeria_EUA0334_combined__sorted.bam\tM_Nigeria_EUA0335_combined__sorted.bam\tM_SierraLeone_AMNH17271_combined__sorted.bam\tM_SierraLeone_AMNH17273_combined__sorted.bam\tXT10_WZ_no_adapt._sorted.bam\tXT11_WW_trim_no_adapt_scafconcat_sorted.bam\tXT1_ZY_no_adapt._sorted.bam\tXT7_WY_no_adapt__sorted.bam\tall_ROM19161_sorted.bam\tall_calcaratus_sorted.bam\tmello_GermSeq_sorted.bam" | cat - ${i} >/tmp/out && mv /tmp/out ${i} ;done
```
3. Then I had to rename first 9 files to get rid of '0' at the begining of combination files
```
mv file01 file1
```
4. Then I made a directory for outputs
```
mkdir outputs
```
5. then ran the following python script ******CHANGING combined_chrs_part***** using the commands in this bash script

```bash
#!/bin/sh
#SBATCH --job-name=bwa_505
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=24:00:00
#SBATCH --mem=64gb
#SBATCH --output=comp_pop.%J.out
#SBATCH --error=comp_pop.%J.err
#SBATCH --account=def-ben
#SBATCH --array=1-46

#SBATCH --mail-user=premacht@mcmaster.ca
#SBATCH --mail-type=BEGIN
#SBATCH --mail-type=END
#SBATCH --mail-type=FAIL
#SBATCH --mail-type=REQUEUE
#SBATCH --mail-type=ALL

module load StdEnv/2020 python/3.8.2
virtualenv --no-download ~/ENV
source ~/ENV/bin/activate
pip install --no-index --upgrade pip
pip install pandas --no-index

python compare_populations.py combined_chrs_part01 my_samp_list_with_sex_and_pop.txt Y N ./splitted_combination_lists/combination${SLURM_ARRAY_TASK_ID} outputs/sample_summary_part1_output${SLURM_ARRAY_TASK_ID}.txt s_list_1.txt s_list_2.txt s_list_3.txt
```
Arguments in the same order are

1. tab file name
2. sample list with sex and population file name
3. Do you want to use all the population combinations? PLease enter Y or N
4. Do you want to run this for all possible combinations (Y/N)? - Had to answer N to use seperate/ multiple sample lists
5. combination list to be used
6. output file name
7. Sample list to be used for pop1
8. Sample list to be used for pop2
9. Sample list to be used for pop3

You can submit all jobs at once with
```
for i in run_comparisons_part*; do sbatch ${i};done
```
# compare_populations.py
```
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
from itertools import chain
import sys


# to get the current working directory
directory = "/scratch/premacht/python_projects_2023/combined_chrs_more_efficient"
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


using_all_combos=sys.argv[3]

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
    # rearrange populations to make sure they are in the proper order
    file_with_locs = file_with_locs.reindex(['#CHROM','POS',p1,p2,p3], axis=1)

    # rearrange populations to make sure they are in the proper order
    file_with_locs = file_with_locs.reindex(['#CHROM','POS',p1,p2,p3],axis=1)

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
    file_with_locs = file_with_locs[file_with_locs.ne('*').all(1)]


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
    #get the summary file name
    summary_saving_name=sys.argv[6]


    # get all the possible populatiopn combinations
    
    import itertools
    
    # ask for population lists to use as pop1 , 2 and 3
    
    s_list_1=sys.argv[7]
    s_list_2=sys.argv[8]
    s_list_3=sys.argv[9]
    
    
    file1 = open(s_list_1, "r")
    list1=list(csv.reader(file1, delimiter="\t"))
    list1=list(chain.from_iterable(list1))
    
    file2 = open(s_list_2, "r")
    list2=list(csv.reader(file2, delimiter="\t"))
    list2=list(chain.from_iterable(list2))
    
    
    file3 = open(s_list_3, "r")
    list3=list(csv.reader(file3, delimiter="\t"))
    list3=list(chain.from_iterable(list3))
    
    pop_lists=[list1,list2,list3]
    
    #pop_lists=[my_samp_list,my_samp_list,my_samp_list]
    
    
    #get all combinations
    all_pop_combinations=list(itertools.product(*pop_lists))
    
    
    #remove duplicates
    combination_list=list({*map(tuple, map(sorted, all_pop_combinations))})

    
    #convert list to df
    combination_list=pd.DataFrame(combination_list)
    
    #drop cols rows with equal vals
    combination_list = combination_list.drop(combination_list[combination_list[0] == combination_list[1]].index)
    
    print('Saving a list of all possible combinations')
    combination_list.to_csv('combination_list.tsv',sep='\t',index=False)
    
    combination_file_desicion=sys.argv[4]
    if combination_file_desicion=='Y':
        combination_file='combination_list.tsv'
    else:
        combination_file=sys.argv[5]
    
    
    
    combination_list=pd.read_table(combination_file)
    combination_list.columns=['p1','p2','p3']
        
    # Create df to store data
    summary_df = pd.DataFrame(columns=['p1','p2','p3','all equal','a=b','a=c','b=c'])
    
    for combo in range(0,len(combination_list)) :
        
    
        p1=combination_list['p1'][combo]
        p2=combination_list['p2'][combo]
        p3=combination_list['p3'][combo]
        
        if p1!=p2 and p1!=p3 and p2!=p3:
            
            print('Running iteration '+str(combo)+' out of total'+str(len(combination_list)))
        
            #avoid using same individual as different pops
            
            print('calculating similarities and differences for '+str(len(combination_list))+ ' combinations. \n\n *****This might take hours depending on the number of populations******')
            print(p1,p2,p3)
    
    
            print('Reading input file')
            
            # read selected columns
            file_with_locs=pd.read_csv(inputfile, sep='\t',usecols=['#CHROM','POS',p1,p2,p3])
            # rearrange populations to make sure they are in the proper order
            file_with_locs = file_with_locs.reindex(['#CHROM','POS',p1,p2,p3], axis=1)
            
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
            file_with_locs = file_with_locs[file_with_locs.ne('*').all(1)]
            
            
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
            summary_df.to_csv(summary_saving_name, sep="\t",index=False)
```
This gives a lot of output files for different parts of the tab file and different individual combinations. You can combine files with all different combinations with following command
I had to use multiple loops here because some numbers had 0 in the begining
```
mkdir combined_summaries
for i in {2..9};do tail -q -n +2 sample_summary_part0$i* > combined_summaries/combined_summary_part_${i}.tab ; done
for i in {10..11};do tail -q -n +2 sample_summary_part$i* > combined_summaries/combined_summary_part_${i}.tab ; done
tail -q -n +2 sample_summary_part1_* > combined_summaries/combined_summary_part_1.tab
```

Then I downloaded all the data and used following r script to arrange and plot data
```
rsync -axvH --no-g --no-p premacht@graham.computecanada.ca:/scratch/premacht/python_projects_2023/combined_chrs_more_efficient/outputs/combined_summaries .
```

```R
library("rstudioapi") 
setwd(dirname(getActiveDocumentContext()$path))
library(forcats)
library(reshape)
library(tidyr)
library(ggplot2)
#install.packages("rlist")
library(rlist)
#install.packages("insight")
library(insight)
#print_color("ERROR", "red")
library(plyr)

# first I have to load all data from different parts and add them together

# **** Chnage the 'range_of_parts' to match the no of parts here

#part 11 didn't have anything and was left out
range_of_parts<-c(1:10)
for (i in range_of_parts) {
  assign(paste('my_dataset_',i,sep = ''),read.csv(paste('./combined_summaries/combined_summary_part_',i,'.tab',sep = ''),sep = '\t',header = FALSE,col.names = c('p1','p2','p3','equal_in_all_pops','Pop_A_equals_Pop_B','Pop_A_equals_Pop_C','Pop_B_equals_Pop_C')))
}

# loading a placeholder df as the sum df(to add all values together)

my_data<-my_dataset_3


#set all of its values to 0

my_data$equal_in_all_pops<-0
my_data$Pop_A_equals_Pop_B<-0
my_data$Pop_A_equals_Pop_C<-0
my_data$Pop_B_equals_Pop_C<-0

#checking for the same order of columns between dfs
for (part in range_of_parts) {
  current_df_name<-paste('my_dataset_',part,sep = '')
  current_df<-get(current_df_name)
  
  checked_list<-purrr::map2_dbl(my_dataset_3$p1, current_df$p1, ~sum(.x == .y))
  allSame <- function(x) length(unique(x)) == 1
  is_similiar<-allSame(checked_list)
  if (is_similiar==TRUE) {
    print(paste('Datase 1 pop1 order is equal to Dataset ',part,'pop1. GOOD TO PROCEED'))
  }
  if(is_similiar==FALSE) {
    print_color(paste('Datase 1 pop1 order is NOT equal to Dataset ',part,'pop1. *****PLEASE CHECK AND RE ARRANGE*****'),'red')
    Sys.sleep(2)
  }
  
  checked_list<-purrr::map2_dbl(my_dataset_3$p2, current_df$p2, ~sum(.x == .y))
  allSame <- function(x) length(unique(x)) == 1
  is_similiar<-allSame(checked_list)
  if (is_similiar==TRUE) {
    print(paste('Datase 1 pop2 order is equal to Dataset ',part,'pop2. GOOD TO PROCEED'))
  }
  if(is_similiar==FALSE) {
    print_color(paste('Datase 1 pop2 order is NOT equal to Dataset ',part,'pop2. *****PLEASE CHECK AND RE ARRANGE*****'),'red')
    Sys.sleep(2)
  }
  
  checked_list<-purrr::map2_dbl(my_dataset_3$p3, current_df$p3, ~sum(.x == .y))
  allSame <- function(x) length(unique(x)) == 1
  is_similiar<-allSame(checked_list)
  if (is_similiar==TRUE) {
    print(paste('Datase 1 pop3 order is equal to Dataset ',part,'pop3. GOOD TO PROCEED'))
  }
  if(is_similiar==FALSE) {
    print_color(paste('Datase 1 pop3 order is NOT equal to Dataset ',part,'pop3. *****PLEASE CHECK AND RE ARRANGE*****'),'red')
    Sys.sleep(2)
  }
}



#sum data for final dataset

for (part in range_of_parts) {
  current_dataset_name<-paste('my_dataset_',part,sep = '')
  current_dataset<-get(current_dataset_name)
  
  my_data$equal_in_all_pops<-my_data$equal_in_all_pops+current_dataset$equal_in_all_pops
  my_data$Pop_A_equals_Pop_B<-my_data$Pop_A_equals_Pop_B+current_dataset$Pop_A_equals_Pop_B
  my_data$Pop_A_equals_Pop_C<-my_data$Pop_A_equals_Pop_C+current_dataset$Pop_A_equals_Pop_C
  my_data$Pop_B_equals_Pop_C<-my_data$Pop_B_equals_Pop_C+current_dataset$Pop_B_equals_Pop_C
}

#saving a copy to proof read

write.table(my_data,file = './combined_dataframe_unarranged.tsv', sep='\t', col.names = NA)



# renaming all samples
my_data[my_data == "F_SierraLeone_AMNH17272_combined__sorted.bam"] <- "SL-F1"
my_data[my_data == "F_SierraLeone_AMNH17274_combined__sorted.bam"] <- "SL-F2"
my_data[my_data == "M_SierraLeone_AMNH17271_combined__sorted.bam"] <- "SL-M1"
my_data[my_data == "M_SierraLeone_AMNH17273_combined__sorted.bam"] <- "SL-M2"
my_data[my_data == "all_ROM19161_sorted.bam"] <- "LB-F1"
my_data[my_data == "F_IvoryCoast_xen228_combined__sorted.bam"] <- "IC-F1"
my_data[my_data == "F_Ghana_WZ_BJE4687_combined__sorted.bam"] <- "GH-F1"
my_data[my_data == "M_Ghana_WY_BJE4362_combined__sorted.bam"] <- "GH-M1"
my_data[my_data == "M_Ghana_ZY_BJE4360_combined__sorted.bam"] <- "GH-M2"
my_data[my_data == "XT10_WZ_no_adapt._sorted.bam"] <- "LT-F1"
my_data[my_data == "XT11_WW_trim_no_adapt_scafconcat_sorted.bam"] <- "LT-F2"
my_data[my_data == "XT1_ZY_no_adapt._sorted.bam"] <- "LT-M1"
my_data[my_data == "XT7_WY_no_adapt__sorted.bam"] <- "LT-M2"
my_data[my_data == "F_Nigeria_EUA0331_combined__sorted.bam"] <- "NG-F1"
my_data[my_data == "F_Nigeria_EUA0333_combined__sorted.bam"] <- "NG-F2"
my_data[my_data == "M_Nigeria_EUA0334_combined__sorted.bam"] <- "NG-M1"
my_data[my_data == "M_Nigeria_EUA0335_combined__sorted.bam"] <- "NG-M2"

#looking for the highest value in both comparisons so I can use the same scale for both


max_y_lim<-max(my_data$Pop_A_equals_Pop_B,my_data$Pop_A_equals_Pop_C,my_data$Pop_B_equals_Pop_C)
upper_y<-(round_any(max_y_lim,1000000))/1000000


#*************seperate cal and mello**************
#*
cal_data <- subset(my_data, p3== 'all_calcaratus_sorted.bam')
mello_data<-subset(my_data, p3== 'mello_GermSeq_sorted.bam')

#************Calcaratus***************************

# now I have to switch columns as needed to create the proper shape for plot set

#make dummy list
new_pop_2<-rep(1: nrow(cal_data))
new_pop_1<-rep(1: nrow(cal_data))

#not changing pop3 anyway
new_pop_3<-cal_data$p3

#this does not change
new_p1_eq_p2<-cal_data$Pop_A_equals_Pop_B

#these do change
new_p1_eq_p3<-rep(1: nrow(cal_data))
new_p2_eq_p3<-rep(1: nrow(cal_data))

#make sample list in the correct order

sample_list<-c('SL-F1',
               'SL-F2',
               'SL-M1',
               'SL-M2',
               'LB-F1',
               'IC-F1',
               'GH-F1',
               'GH-M1',
               'GH-M2',
               'LT-F1',
               'LT-F2',
               'LT-M1',
               'LT-M2',
               'NG-F1',
               'NG-F2',
               'NG-M1',
               'NG-M2')
#reverse sample list to make it upside down triange. *****This is used to adjust the no of comparisons for each sample in next step****
sample_list_reversed<-list.reverse(sample_list)

# now I have to change pop order and values responsible for them accordingly

for (sample in 1:length(sample_list_reversed)) {
  print(sample_list_reversed[sample])
  
  for (location in 1:nrow(cal_data)) {
    if ((cal_data$p1[location])==sample_list_reversed[sample]) {
      new_pop_1[location]<-sample_list_reversed[sample]
      new_pop_2[location]<-cal_data$p2[location]
      new_p1_eq_p3[location]<-cal_data$Pop_A_equals_Pop_C[location]
      new_p2_eq_p3[location]<-cal_data$Pop_B_equals_Pop_C[location]
    }
    else if ((cal_data$p2[location])==sample_list_reversed[sample]) {
      new_pop_1[location]<-sample_list_reversed[sample]
      new_pop_2[location]<-cal_data$p1[location]
      new_p1_eq_p3[location]<-cal_data$Pop_B_equals_Pop_C[location]
      new_p2_eq_p3[location]<-cal_data$Pop_A_equals_Pop_C[location]
    }
  }
  
}


#Create nfinal cal df with arranged data

final_cal_data=data.frame(unlist(new_pop_1),unlist(new_pop_2),unlist(new_pop_3),unlist(new_p1_eq_p2),unlist(new_p1_eq_p3),unlist(new_p2_eq_p3))

#saving a copy to proof read

write.table(final_cal_data,file = './cal_arranged_df.tsv', sep='\t', col.names = NA)

#rename df

colnames(final_cal_data)<-c('p1','p2','p3','A similiar to B','A similiar to C','B similiar to C')

#melt df
cal_data_to_plot <- final_cal_data %>% 
  pivot_longer(
    cols = `A similiar to B`:`A similiar to C`:'B similiar to C', 
    names_to = "Comparison",
    values_to = "value"
  )

# ************************************



#changing column order to make it look better
cal_data_to_plot$p2 <- factor(cal_data_to_plot$p2,levels = sample_list)
cal_data_to_plot$p1 <- factor(cal_data_to_plot$p1,levels = sample_list)

#plot

cal_plot<-ggplot(cal_data_to_plot, aes(x=p2,y = value/1000000,fill=Comparison)) + 
  geom_bar(position="dodge",stat='identity')+ 
  facet_wrap(~ p1,nrow = 16)+
  theme_classic()+
  theme(axis.text.x = element_text(angle = 45, vjust = 1, hjust=1))+
  xlab("Individual B") + ylab("No. of bp (millions)")+ 
  scale_fill_manual(values=c("black", "red","purple"))+
  ylim(0,upper_y)+
  ggtitle("Individual A")+
  theme(plot.title = element_text(hjust = 0.5,size = 10))

ggsave("cal_plot.pdf",cal_plot,width = 10,height = 15)


#************Mellotrop***************************

# now I have to switch columns as needed to create the proper shape for plot set

#make dummy list
new_pop_2<-rep(1: nrow(mello_data))
new_pop_1<-rep(1: nrow(mello_data))

#not changing pop3 anyway
new_pop_3<-mello_data$p3

#this does not change
new_p1_eq_p2<-mello_data$Pop_A_equals_Pop_B

#these do change
new_p1_eq_p3<-rep(1: nrow(mello_data))
new_p2_eq_p3<-rep(1: nrow(mello_data))

#make sample list in the correct order

sample_list<-c('SL-F1',
               'SL-F2',
               'SL-M1',
               'SL-M2',
               'LB-F1',
               'IC-F1',
               'GH-F1',
               'GH-M1',
               'GH-M2',
               'LT-F1',
               'LT-F2',
               'LT-M1',
               'LT-M2',
               'NG-F1',
               'NG-F2',
               'NG-M1',
               'NG-M2')
#reverse sample list to make it upside down triange. *****This is used to adjust the no of comparisons for each sample in next step****
sample_list_reversed<-list.reverse(sample_list)

# now I have to change pop order and values responsible for them accordingly

for (sample in 1:length(sample_list_reversed)) {
  print(sample_list_reversed[sample])
  
  for (location in 1:nrow(mello_data)) {
    if ((mello_data$p1[location])==sample_list_reversed[sample]) {
      new_pop_1[location]<-sample_list_reversed[sample]
      new_pop_2[location]<-mello_data$p2[location]
      new_p1_eq_p3[location]<-mello_data$Pop_A_equals_Pop_C[location]
      new_p2_eq_p3[location]<-mello_data$Pop_B_equals_Pop_C[location]
    }
    else if ((mello_data$p2[location])==sample_list_reversed[sample]) {
      new_pop_1[location]<-sample_list_reversed[sample]
      new_pop_2[location]<-mello_data$p1[location]
      new_p1_eq_p3[location]<-mello_data$Pop_B_equals_Pop_C[location]
      new_p2_eq_p3[location]<-mello_data$Pop_A_equals_Pop_C[location]
    }
  }
  
}


#Create nfinal mello df with arranged data

final_mello_data=data.frame(unlist(new_pop_1),unlist(new_pop_2),unlist(new_pop_3),unlist(new_p1_eq_p2),unlist(new_p1_eq_p3),unlist(new_p2_eq_p3))

#saving a copy to proof read

write.table(final_mello_data,file = './mello_arranged_df.tsv', sep='\t', col.names = NA)

#rename df

colnames(final_mello_data)<-c('p1','p2','p3','A similiar to B','A similiar to C','B similiar to C')

#melt df
mello_data_to_plot <- final_mello_data %>% 
  pivot_longer(
    cols = `A similiar to B`:`A similiar to C`:'B similiar to C', 
    names_to = "Comparison",
    values_to = "value"
  )

# ************************************



#changing column order to make it look better
mello_data_to_plot$p2 <- factor(mello_data_to_plot$p2,levels = sample_list)
mello_data_to_plot$p1 <- factor(mello_data_to_plot$p1,levels = sample_list)

#looking for the highest value in both comparisons so I can use the same smelloe for both

max_mello<-max(mello_data_to_plot$value)
max_mello<-max(mello_data_to_plot$value)
max_y_lim<-max(max_mello,max_mello)
upper_y<-(round_any(max_y_lim,1000000))/1000000

mello_plot<-ggplot(mello_data_to_plot, aes(x=p2,y = value/1000000,fill=Comparison)) + 
  geom_bar(position="dodge",stat='identity')+ 
  facet_wrap(~ p1,nrow = 16)+
  theme_classic()+
  theme(axis.text.x = element_text(angle = 45, vjust = 1, hjust=1))+
  xlab("Individual B") + ylab("No. of bp (millions)")+ 
  scale_fill_manual(values=c("black", "red","purple"))+
  ylim(0,upper_y)+
  ggtitle("Individual A")+
  theme(plot.title = element_text(hjust = 0.5,size = 10))

ggsave("mello_plot.pdf",mello_plot,width = 10,height = 15)


```





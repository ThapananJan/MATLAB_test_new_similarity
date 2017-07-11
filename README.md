clc();

name = importdata('C:\Users\sura\Desktop\Dataset\UCR 47 dataset\47_Trainingset_Name.txt');
[number_of_file nc] = size(name);

for round=1:34
    
file_train = {'C:\Users\sura\Desktop\Dataset\UCR 47 dataset\'};
file_train = strcat(file_train,name{round});
file_train = char(file_train);
file_test = strcat(file_train(1:end-5),'TEST');

Data_train = importdata(file_train);
Data_test = importdata(file_test);
[row_train,column_train] = size(Data_train);
[row_test,column_test] = size(Data_test);
ans_dtw = ones(row_test,1)*9999;
ans_similar = ones(row_test,1)*-9999;

ans_class_dtw = zeros(row_test,1);
ans_class_similar = zeros(row_test,1);

true_dtw = 0;
true_similar = 0;

%%%%%% finding the 1-NN
for i=1:row_test
    for j=1:row_train
        obj = MATLAB_DBA.DTW_distance(Data_train(i,2:column_test),Data_test(j,2:column_train),100);     %% To measure similarity we use test as query. (swapping test or train as a query is not equal).
        if(ans_dtw(i,1) > obj.DTW_answer())
            ans_dtw(i,1) = obj.DTW_answer();
            ans_class_dtw(i,1) = Data_train(j,1);
        end
        if(ans_similar(i,1) < obj.Similarity_answer())
            ans_similar(i,1) = obj.Similarity_answer();
            ans_class_similar(i,1) = Data_train(j,1);
        end
    end
    
    if(ans_class_dtw(i,1) == Data_test(i,1)) 
        true_dtw = true_dtw + 1;
    end
    if(ans_class_similar(i,1) == Data_test(i,1)) 
        true_similar = true_similar + 1;
    end
end



fprintf('%s\tAcc_dtw\t%.3f\tAcc_similar\t%.3f\n',file_test,(true_dtw/row_test),(true_similar/row_test));

end

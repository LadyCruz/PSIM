% Taller 2
clear all;
close all;
clc

%% punto 1

load 'chb01_03_edfm.mat';  % Cargada de Physionet-PhysionetBank ATM

 s_fs = 256;
 s_gain = 2.559375;
 s_base = -1;
 n_crisis = 766976;
 
 segm = ((val (:,n_crisis - s_fs*30:n_crisis + s_fs*30))-s_base)/s_gain;
 
 figure;
 plot(segm(1,:));
 hold on;
 offset = -1000;
 
 for i=2:size(segm,1)
     plot(segm(i,:)+offset);
     offset = offset -1000; 
 end
 
 segm1 = segm(21,:);
 
 %% punto 2
 
 ind = 1;
 j = 1;
 ancho = s_fs*5;
 h_win = length(segm1)/(ancho/2);
 d = 1;
 
 while (d ~= round(h_win))
     v(:,j) = segm1(ind:ind+ancho);
     ind = ind + ancho/2;
     % activacion, mobilidad y complejidad
     [v_act(j), v_mob(j), v_comp(j)] = f_HjorthFeats(v(:,j));
     % dimension fractal
     [HFD] = Higuchi_FD(v(:,j), 5);
     v_hfd(j)= HFD;
     % entropia de shannon
     bins = round((max(v(:,j))-min(v(:,j)))/20);
     hv = histogram(v(:,j),[min(v(:,j)):20:max(v(:,j))]);
     p = hv.Values/sum(hv.Values);
     v_entrop(j)=sum(p.*log(1./p));
     %v_entrop(j)=-sum(p.*log(p));
     j=j+1;
     d=d+1;
 end
 
 %% punto 3
 
 figure;
 subplot(5,1,1);
 plot(v_act);
 title('actividad');
 subplot(5,1,2);
 plot(v_mob);
 title('movilidad');
 subplot(5,1,3);
 plot(v_comp);
 title('complejidad');
 subplot(5,1,4);
 plot(v_hfd);
 title('dimension fractal');
 subplot(5,1,5);
 plot(v_entrop);
 title('entropia');

# DDDS
Drowsy Driver Detection System
function varargout = pro(varargin)
% PRO M-file for pro.fig
% PRO, by itself, creates a new PRO or raises the existing
% singleton*.
%
% H = PRO returns the handle to a new PRO or the handle to
% the existing singleton*.
%
% PRO('CALLBACK',hObject,eventData,handles,...) calls the local
% function named CALLBACK in PRO.M with the given input arguments.
%
% PRO('Property','Value',...) creates a new PRO or raises the
% existing singleton*.  Starting from the left, property value pairs are
% applied to the GUI before pro_OpeningFcn gets called.  An
% unrecognized property name or invalid value makes property application
% stop.  All inputs are passed to pro_OpeningFcn via varargin.
%
% *See GUI Options on GUIDE's Tools menu.  Choose "GUI allows only one
%  instance to run (singleton)".
%
% See also: GUIDE, GUIDATA, GUIHANDLES
 
% Edit the above text to modify the response to help pro
 
% Last Modified by GUIDE v2.5 01-Apr-2012 02:52:09
 
% Begin initialization code - DO NOT EDIT



gui_Singleton = 1;
gui_State = struct('gui_Name', mfilename, ...
                                   'gui_Singleton',gui_Singleton, ...
                   'gui_OpeningFcn',@pro_OpeningFcn,
                   'gui_OutputFcn',@pro_OutputFcn,...

'gui_LayoutFcn',[] , ...

'gui_Callback', []);

if nargin && ischar(varargin{1})
    gui_State.gui_Callback = str2func(varargin{1});
end
 
if nargout
    [varargout{1:nargout}] = gui_mainfcn(gui_State, varargin{:});
else
    gui_mainfcn(gui_State, varargin{:});
end
% End initialization code - DO NOT EDIT
 
 
% --- Executes just before pro is made visible.
function pro_OpeningFcn(hObject, eventdata, handles, varargin)
% This function has no output args, see OutputFcn.
% hObject    handle to figure
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
% varargin   command line arguments to pro (see VARARGIN)
 
% Choose default command line output for pro
handles.output = hObject;
 
% Update handles structure
guidata(hObject, handles);
 
% UIWAIT makes pro wait for user response (see UIRESUME)
% uiwait(handles.figure1);
 
 
% --- Outputs from this function are returned to the command line.
function varargout = pro_OutputFcn(hObject, eventdata, handles) 
% varargout  cell array for returning output args (see VARARGOUT);
% hObject    handle to figure
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
 
% Get default command line output from handles structure
varargout{1} = handles.output;
 
 
% --- Executes on button press in pushbutton5.
function pushbutton5_Callback(hObject, eventdata, handles)
% hObject    handle to pushbutton5 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
pos_size = get(handles.figure1,'Position');
% Call modaldlg with the argument 'Position'.
user_response = modaldlg('Title','Confirm Close');
switch user_response
case {'No'}
    % take no action
case 'Yes'
    % Prepare to close GUI application window
    %                  .
    %                  .
    %                  .
    delete(handles.figure1)
    display Goodbye
end
 
 
% --- Executes on button press in pushbutton1.
function pushbutton1_Callback(hObject, eventdata, handles)
% hObject    handle to pushbutton1 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
%search for hardware drive
 
%search for hardware drive
a = imaqhwinfo;
camera_name  = char(a.InstalledAdaptors(end));
camera_info  = imaqhwinfo(camera_name);
camera_id    = camera_info.DeviceInfo.DeviceID(end);
resolution   = char(camera_info.DeviceInfo.SupportedFormats(end));
vid          = videoinput(camera_name, camera_id, resolution);
%set value of trigger reapet its value may be (0,any integer,inf)
set(vid, 'FramesPerTrigger', Inf);
set(vid, 'ReturnedColorspace', 'rgb');
vid.FrameGrabInterval =30;
start(vid)
A = 1;
c=0;
while(vid.FramesAcquired <=10)
    A = getsnapshot(vid);
 
imshow(A);
    imwrite(A,'a.jpg');
 
%%%%%%%%%%%%%%%% face detection %%%%%%%%%%%%%
z=imread('a.jpg');
data = z;
M = rgb2gray(data);
Q =data(:,:,1);
img = imsubtract(Q,M);
img = medfilt2(img, [4 4]);
img = imadjust(img);
bw = im2bw(img,.3);
BW = imfill(bw,'holes');
BW1 = bwlabel(BW, 8);
stats = regionprops(BW1,['basic']);
N=size(stats);
if (bw==0)
A = getsnapshot(vid);
imwrite(A,'a.jpg');
z=imread('a.jpg');
data = z;
M = rgb2gray(data);
Q =data(:,:,1);
img = imsubtract(Q,M);
img = medfilt2(img, [6 6]);
img = imadjust(img);
bw = im2bw(img,.3);
BW = imfill(bw,'holes');
BW1 = bwlabel(BW, 8);
stats = regionprops(BW1,['basic']);
N=size(stats);
 
end
 
     region = stats(1);
for i = 1 : N
      if stats(i).Area > region.Area
        region = stats(i);
      end
   
end
plot = region.BoundingBox;
region.Area;
 
C=imcrop(data,plot);
%imshow(C);
imshow(data)
if region.Area < 11500
N =imcrop(C,[13 1 70 48]);
imshow(N);
imwrite(N,'b.jpg');
title ('1')
else
    N =imcrop(C,[27 60 69 49]);
imshow(N);
imwrite(N,'b.jpg');
title ('2')
end
%pixval on;
imshow(A);
hold on
rectangle('Position',plot,'EdgeColor','r','LineWidth',5)
drawnow;
  
end
stop(vid);
flushdata(vid);
vid.TimerFcn = {'stop'}; 
 
% --- Executes on button press in pushbutton2.
function pushbutton2_Callback(hObject, eventdata, handles)
% hObject    handle to pushbutton2 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
 
tarname = uigetfile('*.jpg','Select the image');
      pic1 = imread(tarname);
   
data = pic1;
M = rgb2gray(data);
Q =data(:,:,1);
img = imsubtract(Q,M);
imshow(img)
img = medfilt2(img, [4 4]);
img = imadjust(img);
bw = im2bw(img,.7);
BW = imfill(bw,'holes');
%imshow(BW);
BW1 = bwlabel(BW,8);
stats = regionprops(BW1,['basic']);
N=size(stats);
if (bw==0)
%A = getsnapshot(vid);
%imshow(A);
%break;
else
 
     region = stats(1);
for i = 1 : N
      if stats(i).Area > region.Area
        region = stats(i);
      end
   
end
plot = region.BoundingBox;
end
imshow(data);
rectangle('Position',plot,'EdgeColor','g','LineWidth',2)
 

 
 
 
 
 
% --- Executes on button press in pushbutton3.
function pushbutton3_Callback(hObject, eventdata, handles)
% hObject    handle to pushbutton3 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
button = questdlg('size of image must greate than 200 * 240','warning','ok','default');
if button == 'ok'
 
tarname = uigetfile('*.jpg','Select the first image');
tarname1 = uigetfile('*.jpg','Select the second image');
       
%function ait_picmatch(pic1,pic2)
pic1 = imread(tarname);
       pic2 = imread(tarname1);
    %   ait_picmatch(pic1,pic2);
 
[x,y,z] = size(pic1);
if(z==3);
 
    pic1 = rgb2gray(pic1);
end
 
[x,y,z] = size(pic2);
if(z==3);
 
    pic2 = rgb2gray(pic2);
end
 
 
 


%applying edge detection on first picture
%so that we obtain white and black points and edges of the objects present
%in the picture.
 
edge_det_pic1 = edge(pic1,'prewitt');
 
%%applying edge detection on second picture
%so that we obtain white and black points and edges of the objects present
%in the picture.
 
edge_det_pic2 = edge(pic2,'prewitt');
 
%definition of different variables to be used in the code below
 
%output variable if pictures have been matched.
OUTPUT_MESSAGE = ' Hence the pictures have been matched, SAME PICTURES ';
 
%output variable if objects in the whole picture have not been matched.
OUTPUT_MESSAGE2 = ' Hence the pictures have not been matched, DIFFERENT PICTURES ';
 
%initialization of different variables used
matched_data = 0;
white_points = 0;
black_points = 0;
x=0;
y=0;
l=0;
m=0;
 
%for loop used for detecting black and white points in the picture.
for a = 1:1:100
    for b = 1:1:100
        if(edge_det_pic1(a,b)==1)
            white_points = white_points+1;
        else
            black_points = black_points+1;
        end
    end
end
 
%for loop comparing the white (edge points) in the two pictures
for i = 1:1:100
    for j = 1:1:100
        if(edge_det_pic1(i,j)==1)&(edge_det_pic2(i,j)==1)
            matched_data = matched_data+1;
            else
                ;
        end
    end
end
    
 
%calculating percentage matching.
total_data = white_points;
total_matched_percentage = (matched_data/total_data)*100;
 
%outputting the result of the system.
if(total_matched_percentage >= 90)          %can add flexability at this point by reducing the amount of matching.
    
    total_matched_percentage
    OUTPUT_MESSAGE
    errordlg('matched','result message');
else
    
    total_matched_percentage
    OUTPUT_MESSAGE2
    errordlg('not matched','result message');
end
end
 
% --- Executes on button press in pushbutton4.
function pushbutton4_Callback(hObject, eventdata, handles)
% hObject    handle to pushbutton4 (see GCBO)
% eventdata  reserved - to be defined in a future version of MATLAB
% handles    structure with handles and user data (see GUIDATA)
 
%search for hardware drive
a = imaqhwinfo;
camera_name  = char(a.InstalledAdaptors(end));
camera_info  = imaqhwinfo(camera_name);
camera_id    = camera_info.DeviceInfo.DeviceID(end);
resolution   = char(camera_info.DeviceInfo.SupportedFormats(end));
vid          = videoinput(camera_name, camera_id, resolution);
%set value of trigger reapet its value may be (0,any integer,inf)
set(vid, 'FramesPerTrigger', Inf);
set(vid, 'ReturnedColorspace', 'rgb');
vid.FrameGrabInterval =20;
start(vid)
A = 1;
c=0;
while(vid.FramesAcquired <=15)
    A = getsnapshot(vid);
 
imshow(A);
    imwrite(A,'a.jpg');
 
%%%%%%%%%%%%%%%% face detection %%%%%%%%%%%%%
z=imread('a.jpg');
data = z;
M = rgb2gray(data);
Q =data(:,:,1);
img = imsubtract(Q,M);
img = medfilt2(img, [6 6]);
img = imadjust(img);
bw = im2bw(img,.3);
BW = imfill(bw,'holes');
BW1 = bwlabel(BW, 8);
stats = regionprops(BW1,['basic']);
N=size(stats);
 
if (bw==0)
A = getsnapshot(vid);
imwrite(A,'a.jpg');
z=imread('a.jpg');
data = z;
M = rgb2gray(data);
Q =data(:,:,1);
img = imsubtract(Q,M);
img = medfilt2(img, [6 6]);
img = imadjust(img);
bw = im2bw(img,.3);
BW = imfill(bw,'holes');
BW1 = bwlabel(BW, 8);
stats = regionprops(BW1,['basic']);
N=size(stats);
 
end
 
     region = stats(1);
for i = 1 : N
      if stats(i).Area > region.Area
        region = stats(i);
      end
   
end
plot = region.BoundingBox;
region.Area;
 
C=imcrop(data,plot);
%imshow(C);
imshow(data)
if region.Area < 11500
N =imcrop(C,[13 1 70 48]);
%imshow(N);
title ('1')
else
    N =imcrop(C,[27 60 69 49]);
%imshow(N);
%imwrite(N,'b.jpg');
title ('2')
end
%pixval on;
%imshow(A);
hold on
rectangle('Position',plot,'EdgeColor','r','LineWidth',5)
%%%%%%%%%%%%%%%%%%%%%% eye compare %%%%%%%%%%%%%%%%%%%
pic1 = N;
       pic2 = imread('b.jpg');
    %   ait_picmatch(pic1,pic2);
 
[x,y,z] = size(pic1);
if(z==1);
else
    pic1 = rgb2gray(pic1);
end
 
[x,y,z] = size(pic2);
if(z==1);
else
    pic2 = rgb2gray(pic2);
end
 
%applying edge detection on first picture
%so that we obtain white and black points and edges of the objects present
%in the picture.
 
edge_det_pic1 = edge(pic1,'sobel');
 
%%applying edge detection on second picture
%so that we obtain white and black points and edges of the objects present
%in the picture.
 
edge_det_pic2 = edge(pic2,'sobel');
 
%definition of different variables to be used in the code below
 
%output variable if pictures have been matched.
OUTPUT_MESSAGE = ' Hence the pictures have been matched, SAME PICTURES ';
 
%output variable if objects in the whole picture have not been matched.
OUTPUT_MESSAGE2 = ' Hence the pictures have not been matched, DIFFERENT PICTURES ';
 
%initialization of different variables used
matched_data = 0;
white_points = 0;
black_points = 0;
 
x=0;
y=0;
l=0;
m=0;
 
%for loop used for detecting black and white points in the picture.
for a = 1:1:46
    for b = 1:1:70
        if(edge_det_pic1(a,b)==1)
            white_points = white_points+1;
        else
            black_points = black_points+1;
        end
    end
end
 
%for loop comparing the white (edge points) in the two pictures
for i = 1:1:46
    for j = 1:1:70
        
        if(edge_det_pic1(i,j)==1)&(edge_det_pic2(i,j)==1)
            matched_data = matched_data+1;
            else
                ;
        end
    end
end
    
%calculating percentage matching.
total_data = white_points;
total_matched_percentage = (matched_data/total_data)*100;
 
%outputting the result of the system.
if(total_matched_percentage >= 1.3)          %can add flexability at this point by reducing the amount of matching.
    
    total_matched_percentage
    OUTPUT_MESSAGE
    c=0;
else
      total_matched_percentage
    OUTPUT_MESSAGE2
 c=c+1;
 c
while    (c==4 )
c
    c=c-4;   
[y,Fs] = wavread('vipmen');
player = audioplayer(y, Fs);
play(player);
button = questdlg('driver sleeping','warning','ok','default');
if button == 'ok'
stop(player);
end
end
 
end
drawnow;
end
stop(vid);
flushdata(vid);
vid.TimerFcn = {'stop'};

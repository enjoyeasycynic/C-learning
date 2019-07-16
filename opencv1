//====================================================================//
// Created by liheng on 19-3-7.
//Program:动态调节相关参数，查找最优的外参，得到毫米波坐标到图像坐标的转换
//Data:2019.3.7
//Author:liheng
//Version:V1.0
//====================================================================//

#include "typedef.h"
#include<opencv2/opencv.hpp>
#include<iostream>
#include "WaveRadar2Image.h"
#include "CGetVideo.h"

//网口相机
//CGetVideo m_getVideo(18072414); // 网口相机编号:17369069  12.5：18072414
CGetVideo m_getVideo("/home/liheng/car_distance_22m.avi");

void onChange(int value, void* param)
{
	//获取waveX,waveY
	//X:0-10,对应-5--5
	//Y:0-10 对应0-50
	float k_waveX = (-5 - 5.0) / (0 - 10.0);
	float b_waveX = -5.0 - k_waveX * 0;

	//获取pitch,yaw角度
	//0-3000,0对应-30.0;3000对应30.0;
	//x     0       3000.0
	//y    -30.0     30.0
	float k_pitch = (-30.0 - 30.0) / (0 - 3000.0);
	float b_pitch = -30.0 - k_pitch * 0;

	float k_yaw = (-30.0 - 30.0) / (0 - 3000.0);
	float b_yaw = -30.0 - k_yaw * 0;


	float waveX = (float)cv::getTrackbarPos("waveX/m", "TrackBar");
	waveX = k_waveX * waveX + b_waveX;

	float waveY = (float)cv::getTrackbarPos("waveY/m", "TrackBar");

	float pitch = (float)cv::getTrackbarPos("pitch/°(-30°-30°)", "TrackBar");
	pitch = k_pitch * pitch + b_pitch;

	float yaw = (float)cv::getTrackbarPos("yaw/°(-30°-30°)", "TrackBar");
	yaw = k_yaw * yaw + b_yaw;

	//获取theta
	float k_theta = (-30.0 - 30.0) / (0 - 3000.0);
	float b_theta = -30.0 - k_theta * 0;

	float theta = (float)cv::getTrackbarPos("theta/°(-30°-30°)", "TrackBar");
	theta = k_theta * theta + b_theta;

	//====================图像处理========================//
	cv::Mat posInImage, posInCamera;
	cv::Mat src = *(cv::Mat*)param;
	cv::Mat dst; dst.release();
	{
		ADAS::CameraPara cameraPara;
		cameraPara.fu = 2270.512;
		cameraPara.fv = 2271.165;
		cameraPara.cu = 669.0744;//
		cameraPara.cv = 382.6168;
		cameraPara.height = 1750;//mm
		cameraPara.pitch = pitch * (CV_PI * 1.0 / 180.0);
		cameraPara.yaw = yaw * (CV_PI * 1.0 / 180.0);;
		cameraPara.roll = 0;
		cameraPara.image_width = 1280;
		cameraPara.image_height = 720;

		waveY = 22.0;

		cameraPara.camera2wave_radian = theta * (CV_PI * 1.0 / 180.0);
		cameraPara.waveInCamera.x = 0;//mm;
		cameraPara.waveInCamera.y = 0;
		cameraPara.objectHeight = 1000.0;//mm
		cameraPara.objectWidth = 1500.0;//mm



		WaveRadar2Image waveRadar2Image(cameraPara);
		cv::Mat posInWave = (cv::Mat_<float>(2, 1) <<
			waveX,
			waveY);



		waveRadar2Image.TransformWRadar2Image2(posInWave, posInImage, posInCamera);

		dst = src.clone();

		if (dst.channels() == 1)
			cv::cvtColor(dst, dst, CV_GRAY2BGR);

		cv::Mat temp(100, dst.cols, CV_8UC3, cv::Scalar(255, 255, 255));
		cv::vconcat(dst, temp, dst);

		int nObjectNum = posInImage.cols;
		for (int i = 0; i != nObjectNum; ++i)
		{
			cv::rectangle(dst, cv::Rect(posInImage.at<float>(0, i),
				posInImage.at<float>(1, i),
				posInImage.at<float>(2, i),
				posInImage.at<float>(3, i)), cv::Scalar(0, 255, 0), 2);

			cv::circle(dst, cv::Point(posInImage.at<float>(0, i) + posInImage.at<float>(2, i) / 2,
				posInImage.at<float>(1, i) + posInImage.at<float>(3, i) / 2), 3, cv::Scalar(0, 255, 0), 4);

			//std::cout<<"width in pixel="<<posInImage.at<float>(2,i)<<std::endl;
		}



		cv::circle(dst, cv::Point(cameraPara.cu, cameraPara.cv), 6, cv::Scalar(0, 0, 255), 10);
	}




	//cv::rectangle(dst,cv::Point(0,0),cv::Point(220,110),cv::Scalar(255,255,0),1);


	int nHeight = dst.rows;
	int nWidth = dst.cols;
	cv::line(dst, cv::Point(nWidth / 2, 0), cv::Point(nWidth / 2, nHeight), cv::Scalar(0, 0, 255), 2);
	cv::line(dst, cv::Point(0, nHeight / 2), cv::Point(nWidth, nHeight / 2), cv::Scalar(0, 0, 255), 2);

	cv::line(dst, cv::Point(nWidth / 4, 0), cv::Point(nWidth / 4, nHeight), cv::Scalar(0, 0, 255), 1);
	cv::line(dst, cv::Point(nWidth * 3 / 4, 0), cv::Point(nWidth * 3 / 4, nHeight), cv::Scalar(0, 0, 255), 1);


	cv::line(dst, cv::Point(0, nHeight / 4), cv::Point(nWidth, nHeight / 4), cv::Scalar(0, 0, 255), 1);
	cv::line(dst, cv::Point(0, nHeight * 3 / 4), cv::Point(nWidth, nHeight * 3 / 4), cv::Scalar(0, 0, 255), 1);

	char info[256];
	sprintf(info, "waveX:%.2f", waveX);
	cv::putText(dst, info, cv::Point(0, 20), cv::FONT_HERSHEY_SIMPLEX, 0.8, cv::Scalar(0, 255, 0), 2);

	sprintf(info, "waveY:%.2f", waveY);
	cv::putText(dst, info, cv::Point(0, 45), cv::FONT_HERSHEY_SIMPLEX, 0.8, cv::Scalar(0, 255, 0), 2);

	sprintf(info, "theta:%.1f", theta);
	cv::putText(dst, info, cv::Point(0, 70), cv::FONT_HERSHEY_SIMPLEX, 0.8, cv::Scalar(0, 255, 0), 2);

	sprintf(info, "cameraX:%.2f", posInCamera.at<float>(0, 0));
	cv::putText(dst, info, cv::Point(0, 95), cv::FONT_HERSHEY_SIMPLEX, 0.8, cv::Scalar(0, 255, 0), 2);

	sprintf(info, "cameraY:%.2f", posInCamera.at<float>(1, 0));
	cv::putText(dst, info, cv::Point(0, 120), cv::FONT_HERSHEY_SIMPLEX, 0.8, cv::Scalar(0, 255, 0), 2);



	sprintf(info, "pitch:%.1f", pitch);
	cv::putText(dst, info, cv::Point(0, 145), cv::FONT_HERSHEY_SIMPLEX, 0.8, cv::Scalar(0, 255, 0), 2);


	sprintf(info, "yaw:%.1f", yaw);
	cv::putText(dst, info, cv::Point(0, 170), cv::FONT_HERSHEY_SIMPLEX, 0.8, cv::Scalar(0, 255, 0), 2);


	cv::imshow("TrackBar", dst);
}

int main()
{
	cv::Mat src;
	//src = cv::imread("../000658.png",0);//读入灰度图
	cv::namedWindow("TrackBar", CV_WINDOW_AUTOSIZE);

	//创建滑动条
	int waveX = 5;
	cv::createTrackbar("waveX/m", "TrackBar", &waveX, 10, onChange, &src);
	waveX = 5;
	cv::createTrackbar("waveY/m", "TrackBar", &waveX, 10, onChange, &src);

	waveX = 1500;
	cv::createTrackbar("pitch/°(-30°-30°)", "TrackBar", &waveX, 3000, onChange, &src);
	cv::createTrackbar("yaw/°(-30°-30°)", "TrackBar", &waveX, 3000, onChange, &src);

	//毫米波和相机
	waveX = 1500;
	cv::createTrackbar("theta/°(-30°-30°)", "TrackBar", &waveX, 3000, onChange, &src);

	//onChange(0,&src);


	//加载视频进行测试
	//cv::VideoCapture videoCapture;
	//videoCapture.open("/home/zxkj/视频/18_11_11_11_1_59.avi");
	//videoCapture.open("/home/liheng/CLionProjects/kitti/01/left/%06d.png");
	//if( !videoCapture.isOpened() )
	//    return -1;


	int nWaitTime = 0;
	while (true)
	{

		src.release();
		m_getVideo.GetVideoFrame(src);
		//videoCapture >> src;
		if (src.empty())
			break;

		onChange(0, &src);

		char chKey = cv::waitKey(nWaitTime);
		if (chKey == 27) break;
		if (chKey == ' ') nWaitTime = !nWaitTime;


	}

}


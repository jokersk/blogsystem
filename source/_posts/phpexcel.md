---
title: phpExcel
date: 2016-11-10 12:14:00
tags:
---

phpExcel 下載地址
https://phpexcel.codeplex.com/

直接先上代碼

			$objPHPExcel = new PHPExcel();
			$objPHPExcel->setActiveSheetIndex(0);
			$objPHPExcel->getActiveSheet()->setTitle('');

			//這裡取到資料
			$records = $this->db->get("")->result();

			//這裡是excel 的首行的資料
			$header_arr = array();
			
			//把首行放進excel
			$col = 0;
			foreach ($header_arr as $key => $value) {
			$objPHPExcel->getActiveSheet()->setCellValueByColumnAndRow($col++, 1, $value);
			}
			
			//把資料放進excel 這裡用的方法是 setCellValueByColumnAndRow(col , row , value)
			foreach($records as $row=>$record) {
				$col = 0;
				foreach($record as $key=>$value) {
					$objPHPExcel->getActiveSheet()->setCellValueByColumnAndRow($col++, $row+2, $value);
				}
			}
			
			// 這裡的代碼是固定的，用來自動調整格子的大小
			foreach(range('A','Z') as $columnID) {
			   $objPHPExcel->getActiveSheet()->getColumnDimension($columnID)->setAutoSize(true);
			   foreach(range('A','Z') as $columnID2) {
			   	$objPHPExcel->getActiveSheet()->getColumnDimension($columnID.$columnID2)->setAutoSize(true);
			   }
			}
			
			//選擇excel的格式
			//$objWriter = PHPExcel_IOFactory::createWriter($objPHPExcel, 'Excel5');
			$objWriter = PHPExcel_IOFactory::createWriter($objPHPExcel, 'Excel2007');
			
			//這裡就是excel 名 ，結尾可以用 xlsx 或者 xls 
			$filename = date("Y-m-d")."somename.xlsx";

			header('Content-Type: application/vnd.ms-excel');
			
			header('Content-Disposition: attachment;filename='.$filename);
			header('Cache-Control: max-age=0');
			$objWriter->save('php://output');



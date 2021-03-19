#!/bin/bash

#Default values for warn and critcal thresholds
warn_val=20
critical_val=30



return_nrpe(){ 
		return="$1"
		shift 1
		message="$@"

		echo -e "$message"
		if [[ -n $return ]]; then
		exit 0 
		else
		exit $return
		fi
}



parseOpts(){

critcal_flag=false
warn_flag=false

while getopts ":w:c:t" opt; do
  case $opt in
    w)
		warn_val=${OPTARG}
		warn_flag=true
		;;

	c)
        critical_val=${OPTARG}
		critcal_flag=true
		;;
	
	\?)
		echo "Invalid option: -$OPTARG" >&2
		return_nrpe 2
		;;

  esac
done

if ! $warn_flag && ! [[ $warn_val =~ ^-?[0-9]+$ ]];
then
    return_nrpe 2 "-w warning_threshold must be specified with integer argument"
    
fi
if ! $critcal_flag && ! [[ $critical_val =~ ^-?[0-9]+$ ]];
then
    return_nrpe 2 "-c critical_threshold must be specified with integer argument"
fi

}

checkFileCount(){
fileCount=$(lsof | grep beelzebub | egrep /data/ | uniq -c | wc -l)
if [[ -n "${fileCount}" ]]; then
	if [[ "${fileCount}" -lt "${warn_val}" ]]; then
		return_nrpe 0 "[SAFE]:  open file count is  ${fileCount}, which is less than the threshold value of ${warn_val} "
	elif [[ "${fileCount}" -ge "${warn_val}" ]] && [[ "${fileCount}" -le "${critical_val}" ]]; then
		return_nrpe 1 "[WARNING]:  open file count is  ${fileCount}, which is greater than the threshold value of ${warn_val}"
	elif [[ "${fileCount}" -ge "${critical_val}" ]]; then
		return_nrpe 2 "[CRITICAL]: open file count is ${fileCount}, which is greater than the threshold value of ${critical_val}"
	fi	
elif [[ -z "${fileCount}" ]]; then
	return_nrpe 2 "[CRITICAL]: No Transaction file count data"
fi
}

if [[ $# -gt 0 ]]; then
	parseOpts $@
else
	parseOpts -warning ${warn_val} -critical ${critical_val}
fi
checkFileCount

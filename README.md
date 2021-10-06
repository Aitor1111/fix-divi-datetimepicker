
# Fix for Divi DateAndTimePicker

I've found how to solve multiselector date time pickers inside a Divi website with the plugin: Date and Time Picker by Input WP.

## Screenshots

![App Screenshot](https://github.com/Aitor1111/fix-divi-datetimepicker/blob/main/screenshot1.png)
![App Screenshot](https://github.com/Aitor1111/fix-divi-datetimepicker/blob/main/screenshot2.png)



## Steps

Follow the next steps:

```bash
  1. Login to your Wordpress site.
  2. Go to Plugins.
  3. Go to Plugin Editor (on plugins dropdown).
  4. Select "Date and Time Picker by Input WP" on the Dropdown (right-top corner).
  5. On Plugin Files Select: assets/js/dtpicker.js.
  6. Remove all Code and paste the code Below (section Code to Use)👇.
  ```

  WARNING! In implementation for the CSS selectors DO NOT USE gaps between selectors.
  Example of usage:
  ```bash
  my-selector1,my-selector2,name-selector
  ```
    
## Code to Use

```javascript
// trigger datetimepicker on document ready and ajax complete ( when fields are loaded in lightboxes )
jQuery(document)
	.ready(function () {
		dtp_init();
	})
	.ajaxComplete(function () {
		dtp_init();
	});

function dtp_init() {
	var dtselectors = [];
	if(datepickeropts.selector.charAt(0) != '#' && datepickeropts.selector.charAt(0) != '.'){
		var diviIDS = datepickeropts.selector.split(',')
		diviIDS.forEach(id => {
			dtselectors[dtselectors.length] = jQuery('input[data-original_id='+ id +']')
		})
	}else{
		dtselectors = [jQuery(datepickeropts.selector)];
	}
	if (dtselectors.length === 0) {
		return;
	}

	jQuery.datetimepicker.setDateFormatter("moment");

	if (datepickeropts.preventkeyboard == "on") {
		dtselectors.forEach(dtselector => {
			jQuery(dtselector).focus(function () {
				jQuery(this).blur();
			});
			jQuery(dtselector).attr("readonly", "readonly");
		})
	}

	// convert to integer
	datepickeropts.offset = parseInt(datepickeropts.offset);

	// custom times logic
	var logic = function (currentDateTime, $input) {
		var mtime = "";
		var value = $input.val();

		if (value === '') {
			if (datepickeropts.use_max_date_as_default !== 'undefined' && datepickeropts.use_max_date_as_default === "1") {
				value = datepickeropts.max_date;
				datepickeropts.now = value;
			}else{
				value = datepickeropts.min_date;
				datepickeropts.now = value;
			}
		}

		$input.datetimepicker({ value: value });

		if (datepickeropts.minDate === "on") {
			var now = moment(datepickeropts.now, datepickeropts.format).toDate();

			if (currentDateTime.toDateString() === now.toDateString()) {
				var futureh = new Date(now.getTime() + datepickeropts.offset * 60000);
				var mint = datepickeropts.minTime.split(":");

				if (parseInt(futureh.getHours()) > parseInt(mint[0])) {
					mtime = futureh.getHours() + ":" + futureh.getMinutes();
					this.setOptions({
						minTime: mtime
					});
				} else {
					mtime = datepickeropts.minTime;
					this.setOptions({
						minTime: mtime
					});
				}
			} else {
				mtime = datepickeropts.minTime;
				this.setOptions({
					minTime: mtime
				});
			}
		}
	};

	// if there's a predefined set of allowed times
	if (
		datepickeropts.timepicker === "on" &&
		datepickeropts.allowed_times !== ""
	) {
		logic = function (currentDateTime, $input) {
			var value = $input.val();

			if (value === '') {
				value = datepickeropts.min_date;
				datepickeropts.now = value;
			}

			$input.datetimepicker({ value: value });

			if (datepickeropts.minDate === "on") {
				var now = moment(datepickeropts.now, datepickeropts.format).toDate();

				if (currentDateTime.toDateString() === now.toDateString()) {
					var futureh = new Date(now.getTime() + datepickeropts.offset * 60000);
					mtime = futureh.getHours() + ":" + futureh.getMinutes();
					this.setOptions({
						minTime: mtime
					});
				} else {
					mtime = datepickeropts.minTime;
					this.setOptions({
						minTime: mtime
					});
				}
			}

			var atimes = "";
			if (currentDateTime.getDay() == 0 && datepickeropts.sunday_times !== "") {
				atimes = datepickeropts.sunday_times;
				this.setOptions({
					allowTimes: atimes
				});
			} else if (
				currentDateTime.getDay() == 1 &&
				datepickeropts.monday_times !== ""
			) {
				atimes = datepickeropts.monday_times;
				this.setOptions({
					allowTimes: atimes
				});
			} else if (
				currentDateTime.getDay() == 2 &&
				datepickeropts.tuesday_times !== ""
			) {
				atimes = datepickeropts.tuesday_times;
				this.setOptions({
					allowTimes: atimes
				});
			} else if (
				currentDateTime.getDay() == 3 &&
				datepickeropts.wednesday_times !== ""
			) {
				atimes = datepickeropts.wednesday_times;
				this.setOptions({
					allowTimes: atimes
				});
			} else if (
				currentDateTime.getDay() == 4 &&
				datepickeropts.thursday_times !== ""
			) {
				atimes = datepickeropts.thursday_times;
				this.setOptions({
					allowTimes: atimes
				});
			} else if (
				currentDateTime.getDay() == 5 &&
				datepickeropts.friday_times !== ""
			) {
				atimes = datepickeropts.friday_times;
				this.setOptions({
					allowTimes: atimes
				});
			} else if (
				currentDateTime.getDay() == 6 &&
				datepickeropts.saturday_times !== ""
			) {
				atimes = datepickeropts.saturday_times;
				this.setOptions({
					allowTimes: atimes
				});
			} else {
				atimes = datepickeropts.allowed_times;
				this.setOptions({
					allowTimes: atimes
				});
			}

			var minDateTime = new Date(currentDateTime.getTime());
			var maxDateTime = new Date(currentDateTime.getTime());

			// minimum time
			var timeex = atimes[0].split(":");
			minDateTime.setHours(parseInt(timeex[0]), parseInt(timeex[1]));

			if (currentDateTime < minDateTime) {
				formattedDate = moment(minDateTime).format(datepickeropts.format);
				$input.datetimepicker({ value: formattedDate });
			}

			// maximum time
			timeex = atimes[atimes.length - 1].split(":");
			maxDateTime.setHours(parseInt(timeex[0]), parseInt(timeex[1]));

			if (currentDateTime > maxDateTime) {
				formattedDate = moment(maxDateTime).format(datepickeropts.format);
				$input.datetimepicker({ value: formattedDate });
			}
		};
	}

	var jumpToNext = function () {
		//logic to jump to next input here
	};

	var opts = {
		scrollMonth: false,
		i18n: datepickeropts.i18n,
		value: datepickeropts.value,
		format: datepickeropts.format,
		formatDate: datepickeropts.dateformat,
		formatTime: datepickeropts.hourformat,
		inline: datepickeropts.inline == "on",
		theme: datepickeropts.theme,
		timepicker: datepickeropts.timepicker == "on",
		datepicker: datepickeropts.datepicker == "on",
		step: parseInt(datepickeropts.step),
		timepickerScrollbar: true,
		dayOfWeekStart: parseInt(datepickeropts.dayOfWeekStart),
		onChangeDateTime: logic,
		onShow: logic,
		onSelectDate: jumpToNext,
		onSelectTime: jumpToNext,
		validateOnBlur: false //added on 1.7.4 to prevent AM/PM format from jumping to 1h before.
	};

	if (datepickeropts.minTime !== "") {
		opts.minTime = datepickeropts.minTime;
	}

	if (datepickeropts.maxTime !== "") {
		opts.maxTime = datepickeropts.maxTime;
	}

	if (datepickeropts.minDate === "on") {
		if (datepickeropts.value !== "") {
			opts.minDate = datepickeropts.value;
		} else {
			opts.minDate = 0;
		}
	}

	if (datepickeropts.max_date !== "") {
		opts.maxDate = datepickeropts.max_date;

		// set max year
		opts.yearEnd = parseInt(datepickeropts.max_year);
	}

	if (datepickeropts.min_date !== "") {
		opts.minDate = datepickeropts.min_date;

		// set min year
		opts.yearStart = parseInt(datepickeropts.min_year);
	}

	if (datepickeropts.disabled_days !== "") {
		opts.disabledWeekDays = datepickeropts.disabled_days;
	}

	if (datepickeropts.disabled_calendar_days !== "") {
		opts.disabledDates = datepickeropts.disabled_calendar_days;
	}

	if (datepickeropts.allowed_times !== "") {
		opts.allowTimes = datepickeropts.allowed_times;
	}
	dtselectors.forEach(dtselector => {
		jQuery(dtselector)
			.datetimepicker(opts)
			.attr("type", "text");
	})
	jQuery.datetimepicker.setLocale(datepickeropts.locale);
}
```


  

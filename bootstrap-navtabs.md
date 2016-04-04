```html
    <div class="panel panel-default">
        <div class="panel-heading">
            <h3 class="panel-title">{!! trans('texts.invoice_quote_number') !!}</h3>
        </div>
        <div class="panel-body form-padding-right">

            <div role="tabpanel">
                <ul class="nav nav-tabs" role="tablist" style="border: none">
                    <li role="presentation" class="active">
                        <a href="#invoiceNumber" aria-controls="invoiceNumber" role="tab" data-toggle="tab">{{ trans('texts.invoice_number') }}</a>
                    </li>
                    <li role="presentation">
                        <a href="#quoteNumber" aria-controls="quoteNumber" role="tab" data-toggle="tab">{{ trans('texts.quote_number') }}</a>
                    </li>
                    <li role="presentation">
                        <a href="#recurringInvoiceNumber" aria-controls="recurringInvoiceNumber" role="tab" data-toggle="tab">{{ trans('texts.recurring_invoice_number') }}</a>
                    </li>
                </ul>
            </div>
            <div class="tab-content">
                <div role="tabpanel" class="tab-pane active" id="invoiceNumber">
                    <div class="panel-body">
                        {!! Former::inline_radios('invoice_number_type')
                                ->onchange('onInvoiceNumberTypeChange()')
                                ->label(trans('texts.type'))
                                ->radios([
                                    trans('texts.prefix') => ['value' => 'prefix', 'name' => 'invoice_number_type'],
                                    trans('texts.pattern') => ['value' => 'pattern', 'name' => 'invoice_number_type'],
                                ])->check($account->invoice_number_pattern ? 'pattern' : 'prefix') !!}

                        {!! Former::text('invoice_number_prefix')
                                ->addGroupClass('invoice-prefix')
                                ->label(' ') !!}
                        {!! Former::text('invoice_number_pattern')
                                ->appendIcon('question-sign')
                                ->addGroupClass('invoice-pattern')
                                ->label(' ')
                                ->addGroupClass('number-pattern') !!}
                        {!! Former::text('invoice_number_counter')
                                ->label(trans('texts.counter'))
                                ->help(trans('texts.invoice_number_help') . ' ' . 
                                    trans('texts.next_invoice_number', ['number' => $account->previewNextInvoiceNumber()])) !!}

                    </div>
                </div>
                <div role="tabpanel" class="tab-pane" id="quoteNumber">
                    <div class="panel-body">
                        {!! Former::inline_radios('quote_number_type')
                                ->onchange('onQuoteNumberTypeChange()')
                                ->label(trans('texts.type'))
                                ->radios([
                                    trans('texts.prefix') => ['value' => 'prefix', 'name' => 'quote_number_type'],
                                    trans('texts.pattern') => ['value' => 'pattern', 'name' => 'quote_number_type'],
                                ])->check($account->quote_number_pattern ? 'pattern' : 'prefix') !!}

                        {!! Former::text('quote_number_prefix')
                                ->addGroupClass('quote-prefix')
                                ->label(' ') !!}
                        {!! Former::text('quote_number_pattern')
                                ->appendIcon('question-sign')
                                ->addGroupClass('quote-pattern')
                                ->addGroupClass('number-pattern')
                                ->label(' ') !!}
                        {!! Former::text('quote_number_counter')
                                ->label(trans('texts.counter'))
                                ->addGroupClass('pad-checkbox')
                                ->append(Former::checkbox('share_counter')->raw()
                                ->onclick('setQuoteNumberEnabled()') . ' ' . trans('texts.share_invoice_counter'))
                                ->help(trans('texts.quote_number_help') . ' ' . 
                                    trans('texts.next_quote_number', ['number' => $account->previewNextInvoiceNumber(ENTITY_QUOTE)])) !!}


                    </div>
                </div>
                <div role="tabpanel" class="tab-pane" id="recurringInvoiceNumber">
                    <div class="panel-body">

                        {!! Former::text('recurring_invoice_number_prefix')
                                ->label(trans('texts.prefix'))
                                ->help(trans('texts.recurring_invoice_number_prefix_help')) !!}

                    </div>
                </div>
            </div>

        </div>
    </div>
```
